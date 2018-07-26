---
title: Utilizzo Core grafica e animazione di Core in xamarin. IOS
description: Procedura dettagliata, questo articolo illustra come creare un'applicazione che usa Core grafica e animazione di base. Viene illustrato come disegnare sullo schermo in risposta a tocco utente nonché come aggiungere un'animazione di un'immagine da percorrere lungo un percorso.
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: cecfd7f3a9678f298af3ed547aa7b50a18238729
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242004"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>Utilizzo Core grafica e animazione di Core in xamarin. IOS

Per questa procedura dettagliata si userà per disegnare un tracciato tramite Core Graphics in risposta a input tocco. Quindi, si aggiungerà un `CALayer` contenente un'immagine che viene viene animato lungo il percorso.

Lo screenshot seguente mostra l'applicazione completata:

![](graphics-animation-walkthrough-images/00-final-app.png "L'applicazione completata")

Prima di iniziare il download il *GraphicsDemo* esempio che accompagna questa Guida. È possibile scaricarlo [qui](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/) e si trova all'interno di **GraphicsWalkthrough** directory avviare il progetto denominato **GraphicsDemo_starter** facendo doppio clic su di esso, e Aprire il `DemoView` classe.

## <a name="drawing-a-path"></a>Disegnare un tracciato


1. Nelle `DemoView` aggiungere un `CGPath` variabile alla classe e crearne un'istanza nel costruttore. Anche dichiarare due `CGPoint` variabili `initialPoint` e `latestPoint`, che verrà usato per acquisire il punto di tocco da cui è necessario creare il percorso:
    
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

2. Aggiungere le seguenti direttive using:

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. Successivamente, eseguire l'override `TouchesBegan` e `TouchesMoved,` e Aggiungi le implementazioni seguenti per acquisire il punto di tocco iniziale e ogni punto di tocco successive rispettivamente:

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

    `SetNeedsDisplay` verrà chiamato ogni volta che i tocchi spostare affinché `Draw` da chiamare al successivo passaggio di esecuzione ciclo.

4. Verrà aggiunto al percorso in linee di `Draw` metodo e usare una riga tratteggiata rossa con cui tracciare. [Implementare `Draw` ](~/ios/platform/graphics-animation-ios/core-graphics.md) con il codice seguente:

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

Se eseguiamo ora l'applicazione, è possibile toccare questo pulsante per disegnare sullo schermo, come illustrato nello screenshot seguente:

![](graphics-animation-walkthrough-images/01-path.png "Nella schermata di disegno")

## <a name="animating-along-a-path"></a>L'animazione lungo un tracciato

A questo punto è stato implementato il codice per consentire agli utenti di disegnare il percorso, è possibile aggiungere il codice per aggiungere un'animazione a un livello lungo il tracciato disegnato.

1. Aggiungere prima di tutto una [ `CALayer` ](~/ios/platform/graphics-animation-ios/core-animation.md) variabile alla classe e crearla nel costruttore:

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

2. Successivamente, si aggiungerà il livello come un sottolivello del livello della visualizzazione quando l'utente solleva backup il dito dallo schermo. Quindi, si creerà un'animazione tra fotogrammi chiave utilizzando il percorso, l'animazione del livello `Position`.

    A tale scopo è necessario eseguire l'override di `TouchesEnded` e aggiungere il codice seguente:

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

3. Eseguire l'applicazione ora e dopo il disegno, un livello con un'immagine viene aggiunto e verrà trasmesso lungo il tracciato disegnato:

![](graphics-animation-walkthrough-images/00-final-app.png "Un livello con un'immagine viene aggiunto e verrà trasmesso lungo il tracciato disegnato")

## <a name="summary"></a>Riepilogo

In questo articolo si sono esaminati un esempio in cui collegare i concetti di grafica e animazione. In primo luogo, è stato illustrato come utilizzare Core Graphics per disegnare un tracciato un `UIView` in risposta a tocco utente. Quindi è stato illustrato come utilizzare Core Animation per convertire un'immagine percorrere lungo tale percorso.


## <a name="related-links"></a>Collegamenti correlati

- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Recipe di animazione di base](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
