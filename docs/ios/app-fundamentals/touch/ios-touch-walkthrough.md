---
title: 'Procedura dettagliata: utilizzo tocco in iOS'
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 58066ef0071c8105658f0d766e8f038b2bd3ddf2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough--using-touch-in-ios"></a>Procedura dettagliata: utilizzo tocco in iOS

Questa procedura dettagliata viene illustrato come scrivere codice che risponde a diversi tipi di eventi tocco. Ogni esempio è inclusa in una schermata separata:

- [Esempi di tocco](#Touch_Samples) – come rispondere agli eventi di tocco.
- [Esempi di riconoscitore di movimento](#Gesture_Recognizer_Samples) : come usare i riconoscitori di movimento incorporato.
- [Esempio riconoscitore di movimento personalizzato](#Custom_Gesture_Recognizer) : come compilare un riconoscimento di movimenti personalizzati.

Ogni sezione contiene istruzioni per scrivere il codice da zero.
Il [avvio codice di esempio](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) include già una schermata di storyboard e i menu completa:

 [![](ios-touch-walkthrough-images/image3.png "L'esempio include una schermata di menu")](ios-touch-walkthrough-images/image3.png#lightbox)

Seguire le istruzioni seguenti per aggiungere codice per lo storyboard e ottenere informazioni sui diversi tipi di eventi tocco disponibili in iOS. In alternativa, aprire il [esempio completata](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final) per visualizzare tutti gli elementi di lavoro.

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Esempi di tocco

In questo esempio, illustra alcune delle API il tocco. Seguire questi passaggi per aggiungere il codice necessario per implementare eventi tocco:


1. Aprire il progetto **Touch_Start**. Prima di eseguire il progetto per assicurarsi che tutto sia corretto e il tocco di **tocco esempi** pulsante. Vedrai una schermata simile alla seguente (anche se nessuno dei pulsanti funzionerà):
    
    [![](ios-touch-walkthrough-images/image4.png "App di esempio, eseguire con i pulsanti non funzionante")](ios-touch-walkthrough-images/image4.png#lightbox)


1. Modificare il file **TouchViewController.cs** e aggiungere le variabili di due istanza seguenti alla classe `TouchViewController`:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. Implementare il `TouchesBegan` (metodo), come illustrato nel codice seguente:

    ```csharp 
    public override void TouchesBegan(NSSet touches, UIEvent evt)
    {
        base.TouchesBegan(touches, evt);
    
        // If Multitouch is enabled, report the number of fingers down
        TouchStatus.Text = string.Format ("Number of fingers {0}", touches.Count);
    
        // Get the current touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            // Check to see if any of the images have been touched
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Fist image touched
                TouchImage.Image = UIImage.FromBundle("TouchMe_Touched.png");
                TouchStatus.Text = "Touches Began";
            } else if (touch.TapCount == 2 && DoubleTouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Second image double-tapped, toggle bitmap
                if (imageHighlighted)
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
                    TouchStatus.Text = "Double-Tapped Off";
                }
                else
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
                    TouchStatus.Text = "Double-Tapped On";
                }
                imageHighlighted = !imageHighlighted;
            } else if (DragImage.Frame.Contains(touch.LocationInView(View)))
            {
                // Third image touched, prepare to drag
                touchStartedInside = true;
            }
        }
    }
    ```
    
    Questo metodo funziona cercando un `UITouch` dell'oggetto e, se presente, eseguire un'azione in base a dove si è verificato il tocco:

    * _All'interno di TouchImage_ : visualizzare il testo `Touches Began` in un'etichetta e la modifica dell'immagine.
    * _All'interno di DoubleTouchImage_ : modificare l'immagine visualizzata se il movimento di un doppio tocco.
    * _All'interno di DragImage_ : impostare un flag che indica che è stato avviato il tocco. Il metodo `TouchesMoved` utilizzerà questo flag per determinare se `DragImage` deve essere spostata sullo schermo oppure No, come ammettiamo vediamo nel prossimo passaggio.

    Il codice precedente sono disponibili solo singoli elementi, non sia ancora disponibile alcun comportamento se l'utente sta spostando il dito sullo schermo. Per rispondere allo spostamento, implementare `TouchesMoved` come illustrato nel codice seguente:

    ```csharp 
    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchStatus.Text = "Touches Moved";
            }

            //==== IMAGE DRAG
            // check to see if the touch started in the drag me image
            if (touchStartedInside)
            {
                // move the shape
                float offsetX = touch.PreviousLocationInView(View).X - touch.LocationInView(View).X;
                float offsetY = touch.PreviousLocationInView(View).Y - touch.LocationInView(View).Y;
                DragImage.Frame = new RectangleF(new PointF(DragImage.Frame.X - offsetX, DragImage.Frame.Y - offsetY), DragImage.Frame.Size);
            }
        }
    }
    ```

    Questo metodo ottiene un `UITouch` oggetto, quindi controlla per vedere dove si è verificato il tocco. Se si è verificato il tocco `TouchImage`, quindi il testo spostato ritocchi viene visualizzato sullo schermo. 

    Se `touchStartedInside` è true, si sa che l'utente abbia il dito `DragImage` ed è spostarlo. Il codice si sposterà `DragImage` quando l'utente sposta il dito sullo schermo.

1. È necessario gestire il caso, quando l'utente solleva il propria dito sullo schermo o iOS consente di annullare l'evento di tocco. A tale scopo, verrà implementata `TouchesEnded` e `TouchesCancelled` come illustrato di seguito:

    ```csharp
    public override void TouchesCancelled(NSSet touches, UIEvent evt)
    {
        base.TouchesCancelled(touches, evt);
    
        // reset our tracking flags
        touchStartedInside = false;
        TouchImage.Image = UIImage.FromBundle("TouchMe.png");
        TouchStatus.Text = "";
    }
    
    public override void TouchesEnded(NSSet touches, UIEvent evt)
    {
        base.TouchesEnded(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchImage.Image = UIImage.FromBundle("TouchMe.png");
                TouchStatus.Text = "Touches Ended";
            }
        }
        // reset our tracking flags
        touchStartedInside = false;
    }
    ```
    
    Entrambi questi metodi verrà reimpostato il `touchStartedInside` flag su false. `TouchesEnded` viene inoltre visualizzato `TouchesEnded` sullo schermo.

1. A questo punto la schermata toccare esempi è completata. Si noti come la schermata cambia mentre si interagisce con ognuna delle immagini, come illustrato nella schermata seguente:
        
    [![](ios-touch-walkthrough-images/image4.png "La schermata iniziale dell'app")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "La schermata dopo che l'utente trascina un pulsante")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

##  <a name="gesture-recognizer-samples"></a>Esempi di riconoscitore di movimento

Il [precedente sezione](#Touch_Samples) è stato illustrato come trascinare un oggetto sullo schermo usando gli eventi tocco.
In questa sezione è verrà eliminare gli eventi di tocco e viene illustrato come utilizzare i riconoscitori di movimento seguenti:

-  Il `UIPanGestureRecognizer` per il trascinamento di un'immagine sullo schermo.
-  Il `UITapGestureRecognizer` per rispondere a doppie scelte sullo schermo.

Se si esegue il [avvio codice di esempio](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) e fare clic su di **esempi riconoscitore di movimento** pulsante, viene visualizzata la schermata seguente:

 [![](ios-touch-walkthrough-images/image6.png "Fare clic sul pulsante esempi riconoscitore di movimento Visualizza questa schermata")](ios-touch-walkthrough-images/image6.png#lightbox)

Seguire questi passaggi per implementare i riconoscitori di movimento:


1. Modificare il file **GestureViewController.cs** e aggiungere la variabile di istanza seguente:

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    Abbiamo bisogno di questa variabile di istanza per tenere traccia del percorso precedente dell'immagine.
Il riconoscitore di movimento zoom utilizzerà il `originalImageFrame` valore per calcolare l'offset è necessario ridisegnare l'immagine sullo schermo.

1. Aggiungere il seguente metodo al controller:

    ```csharp
    private void WireUpDragGestureRecognizer()
    {
        // Create a new tap gesture
        UIPanGestureRecognizer gesture = new UIPanGestureRecognizer();
    
        // Wire up the event handler (have to use a selector)
        gesture.AddTarget(() => HandleDrag(gesture));  // to be defined
    
        // Add the gesture recognizer to the view
        DragImage.AddGestureRecognizer(gesture);
    }
    ```

    Questo codice crea un `UIPanGestureRecognizer` istanza e lo aggiunge a una vista.
Si noti che viene assegnato una destinazione per l'azione nel formato del metodo `HandleDrag` : questo metodo è fornito nel passaggio successivo.

1. Per implementare HandleDrag, aggiungere il codice seguente al controller:

    ```csharp
    private void HandleDrag(UIPanGestureRecognizer recognizer)
    {
        // If it's just began, cache the location of the image
        if (recognizer.State == UIGestureRecognizerState.Began)
        {
            originalImageFrame = DragImage.Frame;
        }
    
        // Move the image if the gesture is valid
        if (recognizer.State != (UIGestureRecognizerState.Cancelled | UIGestureRecognizerState.Failed
            | UIGestureRecognizerState.Possible))
        {
            // Move the image by adding the offset to the object's frame
            PointF offset = recognizer.TranslationInView(DragImage);
            RectangleF newFrame = originalImageFrame;
            newFrame.Offset(offset.X, offset.Y);
            DragImage.Frame = newFrame;
        }
    }
    ```

    Il codice precedente verrà innanzitutto controllare lo stato di riconoscitore di movimento e quindi spostare l'immagine sullo schermo. Con questo codice, il controller può ora supporta il trascinamento di un'immagine sullo schermo.


1. Aggiungere un `UITapGestureRecognizer` che verrà modificato l'immagine viene visualizzata nel DoubleTouchImage. Aggiungere il metodo seguente per il `GestureViewController` controller:

    ```csharp
    private void WireUpTapGestureRecognizer()
    {
        // Create a new tap gesture
        UITapGestureRecognizer tapGesture = null;
    
        // Report touch
        Action action = () => {
            TouchStatus.Text = string.Format("Image touched at: {0}",tapGesture.LocationOfTouch(0, DoubleTouchImage));
    
            // Toggle the image
            if (imageHighlighted)
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
            }
            else
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
            }
            imageHighlighted = !imageHighlighted;
        };
    
        tapGesture = new UITapGestureRecognizer(action);
    
        // Configure it
        tapGesture.NumberOfTapsRequired = 2;
    
        // Add the gesture recognizer to the view
        DoubleTouchImage.AddGestureRecognizer(tapGesture);
    }
    ```

    Questo codice è molto simile al codice per il `UIPanGestureRecognizer` ma anziché tramite un delegato per la destinazione di cui si sta usando un `Action`. 

1. L'ultimo da fare è modificare `ViewDidLoad` in modo che chiama i metodi che è stato appena aggiunto. Modificare ViewDidLoad in modo che risulti simile nel codice seguente:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        Title = "Gesture Recognizers";
    
        // Save initial state
        originalImageFrame = DragImage.Frame;
    
        WireUpTapGestureRecognizer();
        WireUpDragGestureRecognizer();
    }
    ```

    Si noti anche che si inizializza il valore di `originalImageFrame`.


1. Eseguire l'applicazione e interagire con le due immagini.
Nella schermata seguente è un esempio di tali interazioni:
    
    [![](ios-touch-walkthrough-images/image7.png "Questa schermata mostra un'interazione di trascinamento")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>Riconoscitore di movimento personalizzato

In questa sezione si applicherà i concetti delle sezioni precedenti per generare un riconoscimento di movimenti personalizzati. Il riconoscitore di movimento personalizzato verrà sottoclassi `UIGestureRecognizer`, verrà riconoscere quando l'utente disegna "V" sullo schermo e attivare o disattivare una bitmap. Nella schermata riportata di seguito è riportato un esempio di questa schermata:

 [![](ios-touch-walkthrough-images/image8.png "L'app riconoscerà quando l'utente disegna "V" sullo schermo")](ios-touch-walkthrough-images/image8.png#lightbox)

Seguire questi passaggi per creare un riconoscimento di movimenti personalizzati:


1. Aggiungere una nuova classe al progetto denominato `CheckmarkGestureRecognizer`, rendendola simile al codice seguente:

    ```csharp
    using System;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace Touch
    {
        public class CheckmarkGestureRecognizer : UIGestureRecognizer
        {
            #region Private Variables
            private CGPoint midpoint = CGPoint.Empty;
            private bool strokeUp = false;
            #endregion
    
            #region Override Methods
            /// <summary>
            ///   Called when the touches end or the recognizer state fails
            /// </summary>
            public override void Reset()
            {
                base.Reset();
    
                strokeUp = false;
                midpoint = CGPoint.Empty;
            }
    
            /// <summary>
            ///   Is called when the fingers touch the screen.
            /// </summary>
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                // we want one and only one finger
                if (touches.Count != 1)
                {
                    base.State = UIGestureRecognizerState.Failed;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the touches are cancelled due to a phone call, etc.
            /// </summary>
            public override void TouchesCancelled(NSSet touches, UIEvent evt)
            {
                base.TouchesCancelled(touches, evt);
                // we fail the recognizer so that there isn't unexpected behavior
                // if the application comes back into view
                base.State = UIGestureRecognizerState.Failed;
            }
    
            /// <summary>
            ///   Called when the fingers lift off the screen
            /// </summary>
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                //
                if (base.State == UIGestureRecognizerState.Possible && strokeUp)
                {
                    base.State = UIGestureRecognizerState.Recognized;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the fingers move
            /// </summary>
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                // if we haven't already failed
                if (base.State != UIGestureRecognizerState.Failed)
                {
                    // get the current and previous touch point
                    CGPoint newPoint = (touches.AnyObject as UITouch).LocationInView(View);
                    CGPoint previousPoint = (touches.AnyObject as UITouch).PreviousLocationInView(View);
    
                    // if we're not already on the upstroke
                    if (!strokeUp)
                    {
                        // if we're moving down, just continue to set the midpoint at
                        // whatever point we're at. when we start to stroke up, it'll stick
                        // as the last point before we upticked
                        if (newPoint.X >= previousPoint.X && newPoint.Y >= previousPoint.Y)
                        {
                            midpoint = newPoint;
                        }
                        // if we're stroking up (moving right x and up y [y axis is flipped])
                        else if (newPoint.X >= previousPoint.X && newPoint.Y <= previousPoint.Y)
                        {
                            strokeUp = true;
                        }
                        // otherwise, we fail the recognizer
                        else
                        {
                            base.State = UIGestureRecognizerState.Failed;
                        }
                    }
                }
    
                Console.WriteLine(base.State.ToString());
            }
            #endregion
        }
    }
    ```

    Viene chiamato il metodo Reset quando la `State` proprietà viene modificato in `Recognized` o `Ended`. Questo è il tempo necessario per ripristinare uno stato interno di cui il riconoscitore di movimento personalizzato.
La classe può ora iniziare successivo che l'utente interagisce con l'applicazione e sia pronto a eseguire nuovamente il riconoscimento di movimento.



1. Ora che è stato definito un riconoscimento di movimenti personalizzati (`CheckmarkGestureRecognizer`) modifica la **CustomGestureViewController.cs** file e aggiungere le seguenti variabili di istanza di due:

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. Per creare un'istanza e configurare il riconoscitore di movimento, aggiungere il seguente metodo al controller:

    ```csharp
    private void WireUpCheckmarkGestureRecognizer()
    {
        // Create the recognizer
        checkmarkGesture = new CheckmarkGestureRecognizer();
    
        // Wire up the event handler
        checkmarkGesture.AddTarget(() => {
            if (checkmarkGesture.State == (UIGestureRecognizerState.Recognized | UIGestureRecognizerState.Ended))
            {
                if (isChecked)
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Unchecked.png");
                }
                else
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Checked.png");
                }
                isChecked = !isChecked;
            }
        });
    
        // Add the gesture recognizer to the view
        View.AddGestureRecognizer(checkmarkGesture);
    }
    ```

1. Modifica `ViewDidLoad` in modo che chiami `WireUpCheckmarkGestureRecognizer`, come illustrato nel frammento di codice seguente:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. Eseguire l'applicazione, quindi riprovare a "V" di disegno sullo schermo. Dovrebbe essere visualizzato l'immagine da modificare, come illustrato nelle schermate seguenti:
    
    [![](ios-touch-walkthrough-images/image9.png "Pulsante selezionato")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "Il pulsante non è selezionato")](ios-touch-walkthrough-images/image10.png#lightbox)



Sopra indicate tre sezioni dimostrazione in diversi modi per rispondere al tocco eventi in iOS: mediante gli eventi tocco, i riconoscitori di movimento incorporati, o con un riconoscimento di movimenti personalizzati.



## <a name="related-links"></a>Collegamenti correlati

- [iOS tocco di avvio (esempio)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS tocco finale (esempio)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
