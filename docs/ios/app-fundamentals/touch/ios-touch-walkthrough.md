---
title: 'Procedura dettagliata: Uso di touch in Novell. iOS'
description: Questo documento descrive come gestire il tocco nelle applicazioni Novell. iOS, illustrando le interazioni tocco di esempio, i riconoscitori di movimento e i riconoscitori di movimento personalizzati.
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: d78363bf7d38f19a14f689c2825ea9c9934653e4
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280258"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>Procedura dettagliata: Uso di touch in Novell. iOS

In questa procedura dettagliata viene illustrato come scrivere codice che risponde a diversi tipi di eventi di tocco. Ogni esempio è contenuto in una schermata separata:

- [Esempi di tocco](#Touch_Samples) : come rispondere agli eventi di tocco.
- [Esempi di riconoscimento del movimento](#Gesture_Recognizer_Samples) : come usare i riconoscitori di movimento predefiniti.
- [Esempio di riconoscimento di movimenti personalizzati](#Custom_Gesture_Recognizer) : come creare un riconoscimento di movimento personalizzato.

Ogni sezione contiene istruzioni per scrivere il codice da zero.
Il [codice di esempio iniziale](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start) include già uno storyboard completo e una schermata di menu:

 [![](ios-touch-walkthrough-images/image3.png "La schermata di menu Includi di esempio")](ios-touch-walkthrough-images/image3.png#lightbox)

Seguire le istruzioni riportate di seguito per aggiungere codice allo storyboard e ottenere informazioni sui diversi tipi di eventi di tocco disponibili in iOS. In alternativa, aprire l' [esempio completato](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final) per vedere tutto funziona.

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Esempi di tocco

In questo esempio, vengono illustrate alcune delle API Touch. Per aggiungere il codice necessario per implementare gli eventi Touch, attenersi alla procedura seguente:


1. Aprire il progetto **Touch_Start**. Eseguire prima il progetto per verificare che tutto sia corretto e toccare il pulsante di **esempio touch** . Verrà visualizzata una schermata simile alla seguente (anche se nessuno dei pulsanti funzionerà):

    [![](ios-touch-walkthrough-images/image4.png "App di esempio eseguita con pulsanti non funzionanti")](ios-touch-walkthrough-images/image4.png#lightbox)


1. Modificare il file **TouchViewController.cs** e aggiungere le due variabili di istanza seguenti alla classe `TouchViewController`:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. Implementare il `TouchesBegan` metodo, come illustrato nel codice seguente:

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
    
    Questo metodo funziona controllando la presenza `UITouch` di un oggetto e, se esiste, eseguire un'azione in base al punto in cui si è verificato il tocco:

    - _All'interno di TouchImage_ : Visualizza `Touches Began` il testo in un'etichetta e modifica l'immagine.
    - _All'interno di DoubleTouchImage_ : modificare l'immagine visualizzata se il movimento è un doppio tocco.
    - _All'interno di DragImage_ : impostare un flag che indica che il tocco è stato avviato. Il metodo `TouchesMoved` userà questo flag per determinare se `DragImage` deve essere spostato intorno allo schermo, come si vedrà nel passaggio successivo.

    Il codice precedente riguarda solo i singoli tocchi, non esiste ancora alcun comportamento se l'utente sta muovendo il dito sullo schermo. Per rispondere allo spostamento, implementare `TouchesMoved` come illustrato nel codice seguente:

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

    Questo metodo ottiene un `UITouch` oggetto, quindi controlla la posizione in cui si è verificato il tocco. Se il tocco si è `TouchImage`verificato in, i ritocchi di testo spostati vengono visualizzati sullo schermo. 

    Se `touchStartedInside` è true, sappiamo che l'utente ha il dito ed è in `DragImage` grado di spostarlo. Il codice verrà spostato `DragImage` quando l'utente sposta il dito intorno allo schermo.

1. È necessario gestire il caso in cui l'utente solleva il dito del dito dallo schermo oppure iOS Annulla l'evento Touch. A tale proposito, verrà implementato `TouchesEnded` e `TouchesCancelled` come illustrato di seguito:

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

    In entrambi i modi il `touchStartedInside` flag viene reimpostato su false. `TouchesEnded`viene visualizzata `TouchesEnded` anche sullo schermo.

1. A questo punto la schermata dei campioni di tocco è stata completata. Si noti che la schermata cambia quando si interagisce con ognuna delle immagini, come illustrato nello screenshot seguente:

    [![](ios-touch-walkthrough-images/image4.png "Schermata iniziale dell'app")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "Schermata dopo che l'utente trascina un pulsante")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

## <a name="gesture-recognizer-samples"></a>Esempi di riconoscimento del movimento

Nella [sezione precedente](#Touch_Samples) è stato illustrato come trascinare un oggetto intorno allo schermo utilizzando gli eventi di tocco.
In questa sezione si elimineranno gli eventi touch e si mostrerà come usare i riconoscitori di movimento seguenti:

- Oggetto `UIPanGestureRecognizer` per il trascinamento di un'immagine intorno allo schermo.
- Oggetto `UITapGestureRecognizer` per rispondere a doppi tocchi sullo schermo.

Se si esegue il [codice di esempio iniziale](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start) e si fa clic sul pulsante **Samples Recognizer Samples** , verrà visualizzata la schermata seguente:

 [![](ios-touch-walkthrough-images/image6.png "Facendo clic sul pulsante esempi di riconoscimento movimento viene visualizzata questa schermata")](ios-touch-walkthrough-images/image6.png#lightbox)

Per implementare i riconoscitori di movimento, attenersi alla procedura seguente:


1. Modificare il file **GestureViewController.cs** e aggiungere la variabile di istanza seguente:

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    Questa variabile di istanza è necessaria per tenere traccia della posizione precedente dell'immagine.
Il riconoscitore di movimento Pan utilizzerà il `originalImageFrame` valore per calcolare l'offset necessario per ricreare l'immagine sullo schermo.

1. Aggiungere il metodo seguente al controller:

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

    Questo codice crea un'istanza `UIPanGestureRecognizer` di un'istanza di e la aggiunge a una visualizzazione.
Si noti che viene assegnata una destinazione al gesto sotto forma di metodo `HandleDrag` . questo metodo viene fornito nel passaggio successivo.

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

    Il codice precedente verificherà innanzitutto lo stato del sistema di riconoscimento del movimento, quindi sposterà l'immagine intorno allo schermo. Con questo codice, il controller è ora in grado di supportare il trascinamento di un'immagine intorno allo schermo.


1. Aggiungere un `UITapGestureRecognizer` che cambierà l'immagine visualizzata in DoubleTouchImage. Aggiungere il metodo seguente al `GestureViewController` controller:

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

    Questo codice è molto simile al codice per `UIPanGestureRecognizer` , ma invece di usare un delegato per una destinazione si usa un oggetto. `Action` 

1. L'ultima cosa da fare è modificare `ViewDidLoad` in modo da chiamare i metodi appena aggiunti. Modificare ViewDidLoad in modo che sia simile al codice seguente:

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

    Si noti anche che viene inizializzato il valore `originalImageFrame`di.


1. Eseguire l'applicazione e interagire con le due immagini.
Lo screenshot seguente è un esempio di queste interazioni:
    
    [![](ios-touch-walkthrough-images/image7.png "Questa schermata mostra un'interazione di trascinamento")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>Riconoscimento di movimenti personalizzati

In questa sezione verranno applicati i concetti delle sezioni precedenti per creare un riconoscimento di movimenti personalizzato. Il riconoscitore di movimenti personalizzati sarà sottoclass `UIGestureRecognizer`e rileverà quando l'utente disegna una "V" sullo schermo, quindi attiva o Disabilita una bitmap. Lo screenshot seguente è un esempio di questa schermata:

 [![](ios-touch-walkthrough-images/image8.png "L'app rileverà quando l'utente disegna una \"V\" sullo schermo")](ios-touch-walkthrough-images/image8.png#lightbox)

Seguire questa procedura per creare un riconoscimento di movimento personalizzato:


1. Aggiungere una nuova classe al progetto denominato `CheckmarkGestureRecognizer`e renderla simile al codice seguente:

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

    Il metodo Reset viene chiamato quando la `State` proprietà viene modificata `Recognized` in o `Ended`. Si tratta del momento in cui reimpostare lo stato interno impostato nel riconoscimento di movimento personalizzato.
A questo punto la classe può essere avviata la volta successiva che l'utente interagisce con l'applicazione ed è pronto per ritentare il riconoscimento del movimento.



1. Ora che è stato definito un riconoscimento di movimento personalizzato (`CheckmarkGestureRecognizer`), modificare il file **CustomGestureViewController.cs** e aggiungere le due variabili di istanza seguenti:

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. Per creare un'istanza e configurare il riconoscitore di movimento, aggiungere il metodo seguente al controller:

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

1. Modificare `ViewDidLoad` in modo che chiami `WireUpCheckmarkGestureRecognizer`, come illustrato nel frammento di codice seguente:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. Eseguire l'applicazione e provare a disegnare una "V" sullo schermo. Si noterà che la visualizzazione dell'immagine viene modificata, come illustrato nelle schermate seguenti:
    
    [![](ios-touch-walkthrough-images/image9.png "Pulsante selezionato")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "Pulsante deselezionato")](ios-touch-walkthrough-images/image10.png#lightbox)



Nelle tre sezioni precedenti sono stati illustrati diversi modi per rispondere agli eventi di tocco in iOS: usando gli eventi Touch, i riconoscitori di movimento predefiniti o con un riconoscimento di movimento personalizzato.



## <a name="related-links"></a>Collegamenti correlati

- [iOS touch Start (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start)
- [iOS touch finale (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
