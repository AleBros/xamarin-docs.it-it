---
title: Utilizzo dei componenti di base grafica e animazione Core in xamarin. IOS
description: In questo articolo dettagliata viene illustrato come creare un'applicazione che utilizza Core grafica e animazione Core. Viene illustrato come disegnare sullo schermo in risposta a tocco utente nonché come aggiungere un'animazione a un'immagine da percorrere lungo un percorso.
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 7a4399a5d62e2000c2a15a65da8e0e427dc039e0
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787055"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>Utilizzo dei componenti di base grafica e animazione Core in xamarin. IOS

Per questa procedura dettagliata verrà per disegnare un tracciato con grafica di base in risposta a input tocco. Quindi, si aggiungerà un `CALayer` contenente un'immagine che si sarà animare lungo il percorso.

La schermata seguente mostra l'applicazione completata:

![](graphics-animation-walkthrough-images/00-final-app.png "Applicazione completata")

Prima di iniziare il download di *GraphicsDemo* esempio in questa Guida. Può essere scaricato [qui](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/) e si trova all'interno di **GraphicsWalkthrough** directory avviare il progetto denominato **GraphicsDemo_starter** facendo doppio clic su di esso, e Aprire la `DemoView` classe.

## <a name="drawing-a-path"></a>Disegnare un tracciato


1. In `DemoView` aggiungere un `CGPath` variabile alla classe e un'istanza nel costruttore. Dichiarare anche due `CGPoint` variabili, `initialPoint` e `latestPoint`, che verrà utilizzato per acquisire il punto di contatto da cui è necessario creare il percorso:
    
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

3. Successivamente, eseguire l'override `TouchesBegan` e `TouchesMoved,` e aggiungere le implementazioni per acquisire il punto di contatto iniziale e ogni punto di contatto successivi rispettivamente seguenti:

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

    `SetNeedsDisplay` verrà chiamato ogni volta che passa ritocchi affinché `Draw` venga chiamata per il successivo ciclo di esecuzione.

4. Verrà aggiunto al percorso in righe di `Draw` metodo e utilizzare una riga tratteggiata rossa da disegnare con. [Implementare `Draw` ](~/ios/platform/graphics-animation-ios/core-graphics.md) con il codice riportato di seguito:

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

Se si esegue ora l'applicazione, è possibile toccare per disegnare sullo schermo, come illustrato nella schermata seguente:

![](graphics-animation-walkthrough-images/01-path.png "Disegno sullo schermo")

## <a name="animating-along-a-path"></a>Animazione lungo un percorso

Ora che sono stati implementati nel codice per consentire agli utenti disegnare il tracciato, aggiungere il codice per aggiungere un'animazione a un livello lungo il percorso disegnato.

1. Aggiungere innanzitutto un [ `CALayer` ](~/ios/platform/graphics-animation-ios/core-animation.md) variabile alla classe e crearne una nel costruttore:

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

2. Successivamente, il livello verrà aggiunto come un sottolivello del livello della vista quando l'utente alza il dito dallo schermo. Quindi, si creerà un'animazione con fotogramma chiave usando il percorso, il livello di animazione `Position`.

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

3. Eseguire l'applicazione ora e dopo il disegno, un livello con un'immagine viene aggiunto e viene trasferito nel percorso creato:

![](graphics-animation-walkthrough-images/00-final-app.png "Un livello con un'immagine viene aggiunto e viene trasferito nel percorso disegnato")

## <a name="summary"></a>Riepilogo

In questo articolo viene incrementata passo passo attraverso un esempio che i concetti di grafica e animazione collegati. In primo luogo, abbiamo anche mostrato come utilizzare grafica di base per disegnare un tracciato in un `UIView` in risposta a tocco utente. Quindi si viene illustrato come utilizzare animazione di base per creare un'immagine lungo tale percorso.


## <a name="related-links"></a>Collegamenti correlati

- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Recipe animazione core](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
