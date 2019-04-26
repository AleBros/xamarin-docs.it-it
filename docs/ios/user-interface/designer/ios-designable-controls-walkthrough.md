---
title: Uso di controlli personalizzati con iOS Designer
description: Questo documento descrive come creare un controllo personalizzato e usarlo con la finestra di progettazione di Xamarin per iOS. Viene illustrato come rendere disponibile il controllo nella casella degli strumenti della finestra di progettazione iOS, implementare il controllo in modo da poter sviluppare correttamente e la progettazione e altre funzionalità.
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 98504c9d5f210d55a2be4c85c52d4bc1418fc223
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61154426"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>Uso di controlli personalizzati con iOS Designer

## <a name="requirements"></a>Requisiti

La finestra di progettazione di Xamarin per iOS è disponibile in Visual Studio per Mac e Visual Studio 2017 e versioni successive su Windows.

Questa guida presuppone una familiarità con il contenuto illustrato nel [Guida introduttiva descrive](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Procedura dettagliata

> [!IMPORTANT]
> Il modo in cui vengono creati i controlli personalizzati a partire da xamarin Studio 5.5, è leggermente diverso rispetto a versioni precedenti. Per creare un controllo personalizzato, ovvero il `IComponent` interfaccia è necessaria (con i metodi di implementazione associata) o la classe può essere annotata con `[DesignTimeVisible(true)]`. Il secondo metodo è utilizzato nell'esempio seguente questa procedura dettagliata.


1. Creare una nuova soluzione dal **iOS > App > Single View Application > c#** modello, denominarlo `ScratchTicket`e continuare tramite la procedura guidata nuovo progetto:

    [![](ios-designable-controls-walkthrough-images/01new.png "Creare una nuova soluzione")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. Creare un nuovo file di classe vuota denominato `ScratchTicketView`:

    [![](ios-designable-controls-walkthrough-images/02new.png "Creare una nuova classe ScratchTicketView")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. Aggiungere il codice seguente per `ScratchTicketView` classe:

    ```csharp
    using System;
    using System.ComponentModel;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace ScratchTicket
    {
        [Register("ScratchTicketView"), DesignTimeVisible(true)]
        public class ScratchTicketView : UIView
        {
            CGPath path;
            CGPoint initialPoint;
            CGPoint latestPoint;
            bool startNewPath = false;
            UIImage image;
    
            [Export("Image"), Browsable(true)]
            public UIImage Image
            {
                get { return image; }
                set
                {
                    image = value;
                    SetNeedsDisplay();
                }
            }
    
            public ScratchTicketView(IntPtr p)
                : base(p)
            {
                Initialize();
            }
    
            public ScratchTicketView()
            {
                Initialize();
            }
    
            void Initialize()
            {
                initialPoint = CGPoint.Empty;
                latestPoint = CGPoint.Empty;
                BackgroundColor = UIColor.Clear;
                Opaque = false;
                path = new CGPath();
                SetNeedsDisplay();
            }
    
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                var touch = touches.AnyObject as UITouch;
    
                if (touch != null)
                {
                    initialPoint = touch.LocationInView(this);
                }
            }
    
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                var touch = touches.AnyObject as UITouch;
    
                if (touch != null)
                {
                    latestPoint = touch.LocationInView(this);
                    SetNeedsDisplay();
                }
            }
    
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                startNewPath = true;
            }
    
            public override void Draw(CGRect rect)
            {
                base.Draw(rect);
    
                using (var g = UIGraphics.GetCurrentContext())
                {
                    if (image != null)
                        g.SetFillColor((UIColor.FromPatternImage(image).CGColor));
                    else
                        g.SetFillColor(UIColor.LightGray.CGColor);
                    g.FillRect(rect);
    
                    if (!initialPoint.IsEmpty)
                    {
                        g.SetLineWidth(20);
                        g.SetBlendMode(CGBlendMode.Clear);
                        UIColor.Clear.SetColor();
    
                        if (path.IsEmpty || startNewPath)
                        {
                            path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                            startNewPath = false;
                        }
                        else
                        {
                            path.AddLineToPoint(latestPoint);
                        }
    
                        g.SetLineCap(CGLineCap.Round);
                        g.AddPath(path);        
                        g.DrawPath(CGPathDrawingMode.Stroke);
                    }
                }
            }
        }
    }
    ```


1. Aggiungere il `FillTexture.png`, `FillTexture2.png` e `Monkey.png` i file (disponibile [da GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) per il **risorse** cartella.
    
1. Fare doppio clic il `Main.storyboard` file per aprirlo nella finestra di progettazione:

    [![](ios-designable-controls-walkthrough-images/03new.png "IOS Designer")](ios-designable-controls-walkthrough-images/03new.png#lightbox)


1. Trascinamento della selezione un **Visualizza immagine** dalle **della casella degli strumenti** alla visualizzazione nello storyboard.

    [![](ios-designable-controls-walkthrough-images/04new.png "Visualizzazione di un'immagine aggiunti al layout")](ios-designable-controls-walkthrough-images/04new.png#lightbox)


1. Selezionare il **Visualizza immagine** e modificare relativo **immagine** proprietà `Monkey.png`.

    [![](ios-designable-controls-walkthrough-images/05new.png "Impostazione di proprietà di immagine della visualizzazione immagine su Monkey.png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)

    
1. Perché si usa le classi di dimensioni è necessario limitare la visualizzazione di immagini. Fare clic sull'immagine di due volte per inserirlo nella modalità di vincolo. Verrà ora vincolarla in Centro facendo l'handle del centro di blocco e lo allinea sia verticalmente che orizzontalmente:

    [![](ios-designable-controls-walkthrough-images/06new.png "Centrare l'immagine")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. Per vincolare l'altezza e la larghezza, fare clic sugli handle di dimensioni di blocco (gli handle a forma di "ossa") e selezionare la larghezza e altezza rispettivamente:

    [![](ios-designable-controls-walkthrough-images/07new.png "Aggiunta di vincoli")](ios-designable-controls-walkthrough-images/07new.png#lightbox)


1. Aggiornare il frame basato sui vincoli facendo clic sul pulsante Aggiorna sulla barra degli strumenti:

    [![](ios-designable-controls-walkthrough-images/08new.png "La barra degli strumenti di vincoli")](ios-designable-controls-walkthrough-images/08new.png#lightbox)


1. Successivamente, compilare il progetto in modo che il **Scratch Visualizza Ticket** verrà visualizzato sotto **componenti personalizzati** della casella degli strumenti:

    [![](ios-designable-controls-walkthrough-images/09new.png "La casella degli strumenti di componenti personalizzati")](ios-designable-controls-walkthrough-images/09new.png#lightbox)


1. Trascinare e rilasciare un **Scratch Visualizza Ticket** in modo che venga visualizzato sull'immagine monkey. Regolare gli handle di trascinamento in modo che la visualizzazione di Ticket Scratch copre il monkey completamente, come illustrato di seguito:

    [![](ios-designable-controls-walkthrough-images/10new.png "Una visualizzazione di file temporanei Ticket tramite la visualizzazione di immagini")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. Limitare la visualizzazione di Ticket dei file temporanei per la visualizzazione di immagini trascinando un rettangolo di delimitazione per selezionare entrambe le visualizzazioni. Selezionare le opzioni per vincolarla in frame larghezza, altezza, Center e centro e update in base ai vincoli, come illustrato di seguito:

    [![](ios-designable-controls-walkthrough-images/11new.png "Allineamento al centro dei e aggiunta di vincoli")](ios-designable-controls-walkthrough-images/11new.png#lightbox)


1. Eseguire l'applicazione e "zero" off sull'immagine per rivelare il monkey.

    [![](ios-designable-controls-walkthrough-images/10-app.png "Esecuzione di un'app di esempio")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>Aggiunta di proprietà Design-Time

La finestra di progettazione include anche il supporto in fase di progettazione per i controlli personalizzati del tipo numeric proprietà enumerazione, string, bool, CGSize, le proprietà UIColor e UIImage. Per una dimostrazione, è possibile aggiungere una proprietà di `ScratchTicketView` per impostare l'immagine che è "grattato off."

Aggiungere il codice seguente per il `ScratchTicketView` classe per la proprietà:

```csharp
[Export("Image"), Browsable(true)]
public UIImage Image 
{
    get { return image; }
    set { 
            image = value;
            SetNeedsDisplay ();
        }
}
```

Potremmo anche voler aggiungere un controllo null per il `Draw` metodo, come illustrato di seguito:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (var g = UIGraphics.GetCurrentContext())
    {
        if (image != null)
            g.SetFillColor ((UIColor.FromPatternImage (image).CGColor));
        else
            g.SetFillColor (UIColor.LightGray.CGColor);
            
        g.FillRect(rect);

        if (!initialPoint.IsEmpty)
        {
             g.SetLineWidth(20);
             g.SetBlendMode(CGBlendMode.Clear);
             UIColor.Clear.SetColor();

             if (path.IsEmpty || startNewPath)
             {
                 path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                 startNewPath = false;
             }
             else
             {
                 path.AddLineToPoint(latestPoint);
             }

             g.SetLineCap(CGLineCap.Round);
             g.AddPath(path);       
             g.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Tra cui un' `ExportAttribute` e una `BrowsableAttribute` con l'argomento impostato su `true` comporterà la proprietà viene visualizzata nella finestra di progettazione **proprietà** pannello. Modifica la proprietà su un'altra immagine inclusa nel progetto, ad esempio `FillTexture2.png`, comporta l'aggiornamento di controllo in fase di progettazione, come illustrato di seguito:

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "Modifica di proprietà in fase di progettazione")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>Riepilogo

In questo articolo viene descritto come creare un controllo personalizzato e come usarla in un'applicazione iOS con iOS designer. È stato illustrato come creare e compilare il controllo per renderlo disponibile per un'applicazione nella finestra di progettazione **casella degli strumenti**. Inoltre, è stato illustrato come implementare il controllo in modo che esegue il rendering correttamente in fase di progettazione e runtime, nonché come esporre le proprietà di controllo personalizzato nella finestra di progettazione.



## <a name="related-links"></a>Collegamenti correlati

- [ScratchTicket (sample)](https://developer.xamarin.com/samples/monotouch/ScratchTicket/)
- [immagini necessarie (esempio)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
