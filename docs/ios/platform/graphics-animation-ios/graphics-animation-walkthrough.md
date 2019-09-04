---
title: Uso della grafica principale e dell'animazione principale in Novell. iOS
description: Questo articolo illustra passo per passo come creare un'applicazione che usa la grafica di base e l'animazione principale. Viene illustrato come creare sullo schermo in risposta a tocco utente e come animare un'immagine per spostarsi lungo un tracciato.
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: badb65ace8d2ab68e102c9be127abe998a602091
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70225857"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>Uso della grafica principale e dell'animazione principale in Novell. iOS

Per questa procedura dettagliata verrà disegnato un percorso usando la grafica principale in risposta all'input tocco. Si aggiungerà quindi un oggetto `CALayer` contenente un'immagine che verrà animata lungo il tracciato.

La schermata seguente illustra l'applicazione completata:

![](graphics-animation-walkthrough-images/00-final-app.png "Applicazione completata")

Prima di iniziare, scaricare l'esempio *GraphicsDemo* che accompagna questa guida. È possibile scaricarlo [qui](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation) e si trova all'interno della directory **GraphicsWalkthrough** avviare il progetto denominato **GraphicsDemo_starter** facendo doppio clic su di esso e aprendo la `DemoView` classe.

## <a name="drawing-a-path"></a>Disegno di un tracciato


1. In `DemoView` aggiungere una `CGPath` variabile alla classe e crearne un'istanza nel costruttore. Dichiarare inoltre due `CGPoint` variabili, `initialPoint` e `latestPoint`, che si utilizzeranno per acquisire il punto di tocco da cui si costruisce il percorso:

    ```csharp
    public class DemoView : UIView
    {
        CGPath path;
        CGPoint initialPoint;
        CGPoint latestPoint;

        public DemoView ()
        {
            BackgroundColor = UIColor.White;

            path = new CGPath ();
        }
    }
    ```

2. Aggiungere le direttive using seguenti:

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. Successivamente, eseguire `TouchesBegan` l' `TouchesMoved,` override di e e aggiungere le implementazioni seguenti per acquisire rispettivamente il punto di tocco iniziale e ogni punto di tocco successivo:

    ```csharp
    public override void TouchesBegan (NSSet touches, UIEvent evt){

        base.TouchesBegan (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt){

        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }
    ```

    `SetNeedsDisplay`verrà chiamato ogni volta `Draw` che si tocca lo spostamento in modo che venga chiamato al passaggio successivo del ciclo di esecuzione.

4. Si aggiungeranno righe al percorso nel `Draw` metodo e si userà una linea rossa tratteggiata per il tracciamento. [ Implementare`Draw` ](~/ios/platform/graphics-animation-ios/core-graphics.md) con il codice riportato di seguito:

    ```csharp
    public override void Draw (CGRect rect){

        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            //get graphics context
            using(CGContext g = UIGraphics.GetCurrentContext ()){

                //set up drawing attributes
                g.SetLineWidth (2);
                UIColor.Red.SetStroke ();

                //add lines to the touch points
                if (path.IsEmpty) {
                    path.AddLines (new CGPoint[]{initialPoint, latestPoint});
                } else {
                    path.AddLineToPoint (latestPoint);
                }

                //use a dashed line
                g.SetLineDash (0, new nfloat[] { 5, 2 * (nfloat)Math.PI });

                //add geometry to graphics context and draw it
                g.AddPath (path);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
    ```

Se l'applicazione viene eseguita adesso, è possibile toccare per creare lo schermo, come illustrato nello screenshot seguente:

![](graphics-animation-walkthrough-images/01-path.png "Disegno sullo schermo")

## <a name="animating-along-a-path"></a>Animazione lungo un tracciato

Ora che è stato implementato il codice per consentire agli utenti di disegnare il percorso, aggiungere il codice per animare un livello lungo il tracciato disegnato.

1. In primo luogo, [`CALayer`](~/ios/platform/graphics-animation-ios/core-animation.md) aggiungere una variabile alla classe e crearla nel costruttore:

    ```csharp
    public class DemoView : UIView
        {
            …

            CALayer layer;

            public DemoView (){
                …

                //create layer
                layer = new CALayer ();
                layer.Bounds = new CGRect (0, 0, 50, 50);
                layer.Position = new CGPoint (50, 50);
                layer.Contents = UIImage.FromFile ("monkey.png").CGImage;
                layer.ContentsGravity = CALayer.GravityResizeAspect;
                layer.BorderWidth = 1.5f;
                layer.CornerRadius = 5;
                layer.BorderColor = UIColor.Blue.CGColor;
                layer.BackgroundColor = UIColor.Purple.CGColor;
            }
    ```

2. Successivamente, si aggiungerà il livello come sottolivello del livello della visualizzazione quando l'utente solleva il dito dallo schermo. Viene quindi creata un'animazione con fotogramma chiave utilizzando il percorso, animando l'oggetto `Position`del livello.

    A tale scopo `TouchesEnded` , è necessario eseguire l'override di e aggiungere il codice seguente:

    ```csharp
    public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            base.TouchesEnded (touches, evt);

            //add layer with image and animate along path

            if (layer.SuperLayer == null)
                Layer.AddSublayer (layer);

            // create a keyframe animation for the position using the path
            layer.Position = latestPoint;
            CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
            animPosition.Path = path;
            animPosition.Duration = 3;
            layer.AddAnimation (animPosition, "position");
        }
    ```

3. Eseguire l'applicazione ora e dopo il disegno, viene aggiunto un livello con un'immagine e viene spostata lungo il tracciato disegnato:

![](graphics-animation-walkthrough-images/00-final-app.png "Un livello con un'immagine viene aggiunto e viaggia lungo il tracciato disegnato")

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato un esempio in cui sono stati associati i concetti grafici e di animazione. In primo luogo, è stato illustrato come usare la grafica di base per tracciare un percorso in un `UIView` in risposta al tocco utente. È stato quindi illustrato come usare l'animazione di base per fare in modo che un'immagine viaggi lungo tale percorso.


## <a name="related-links"></a>Collegamenti correlati

- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Ricette di animazione Core](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
