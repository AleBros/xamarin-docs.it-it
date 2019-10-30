---
title: Uso della grafica principale e dell'animazione principale in Novell. iOS
description: Questo articolo illustra passo per passo come creare un'applicazione che usa la grafica di base e l'animazione principale. Viene illustrato come creare sullo schermo in risposta a tocco utente e come animare un'immagine per spostarsi lungo un tracciato.
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: b35e88cfdc0bce321068951f1617885c90331c83
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032445"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>Uso della grafica principale e dell'animazione principale in Novell. iOS

Per questa procedura dettagliata verrà disegnato un percorso usando la grafica principale in risposta all'input tocco. Si aggiungerà quindi un `CALayer` contenente un'immagine che verrà animata lungo il tracciato.

La schermata seguente illustra l'applicazione completata:

![](graphics-animation-walkthrough-images/00-final-app.png "The completed application")

Prima di iniziare, scaricare l'esempio *GraphicsDemo* che accompagna questa guida. È possibile scaricarlo [qui](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation) e si trova all'interno della directory **GraphicsWalkthrough** avviare il progetto denominato **GraphicsDemo_starter** facendo doppio clic su di esso e aprire la classe `DemoView`.

## <a name="drawing-a-path"></a>Disegno di un tracciato

1. In `DemoView` aggiungere una variabile `CGPath` alla classe e crearne un'istanza nel costruttore. Dichiarare inoltre due `CGPoint` Variables, `initialPoint` e `latestPoint`, che utilizzeremo per acquisire il punto di tocco da cui costruiamo il percorso:

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

3. Eseguire quindi l'override `TouchesBegan` e `TouchesMoved,` e aggiungere le implementazioni seguenti per acquisire rispettivamente il punto di tocco iniziale e ogni punto di tocco successivo:

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

    `SetNeedsDisplay` verrà chiamato ogni volta che si sposta il tocco in modo che `Draw` venga chiamato al passaggio successivo del ciclo di esecuzione.

4. Si aggiungeranno righe al percorso nel metodo `Draw` e si userà una linea rossa tratteggiata per il tracciamento. [Implementare `Draw`](~/ios/platform/graphics-animation-ios/core-graphics.md) con il codice riportato di seguito:

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

![](graphics-animation-walkthrough-images/01-path.png "Drawing on the screen")

## <a name="animating-along-a-path"></a>Animazione lungo un tracciato

Ora che è stato implementato il codice per consentire agli utenti di disegnare il percorso, aggiungere il codice per animare un livello lungo il tracciato disegnato.

1. Aggiungere innanzitutto una variabile [`CALayer`](~/ios/platform/graphics-animation-ios/core-animation.md) alla classe e crearla nel costruttore:

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

2. Successivamente, si aggiungerà il livello come sottolivello del livello della visualizzazione quando l'utente solleva il dito dallo schermo. Viene quindi creata un'animazione con fotogramma chiave utilizzando il percorso, animando la `Position`del livello.

    A tale scopo, è necessario eseguire l'override del `TouchesEnded` e aggiungere il codice seguente:

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

![](graphics-animation-walkthrough-images/00-final-app.png "A layer with an image is added and travels along the drawn path")

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato un esempio in cui sono stati associati i concetti grafici e di animazione. In primo luogo, è stato illustrato come usare la grafica di base per tracciare un percorso in un `UIView` in risposta al tocco utente. È stato quindi illustrato come usare l'animazione di base per fare in modo che un'immagine viaggi lungo tale percorso.

## <a name="related-links"></a>Collegamenti correlati

- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Ricette di animazione Core](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
