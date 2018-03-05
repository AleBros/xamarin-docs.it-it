---
title: 'Procedura dettagliata: utilizzo di controlli personalizzati con la finestra di progettazione di Xamarin per iOS'
description: "Questo articolo fornisce una procedura dettagliata che illustra come creare un controllo personalizzato e utilizzarlo nella finestra di progettazione di Xamarin per iOS. Viene illustrato come creare un controllo disponibile nella casella degli strumenti della finestra di progettazione in modo che può essere trascinamento/rilasciato in una vista. Inoltre, viene illustrato come implementare un controllo in modo corretto rendering in fase di progettazione e in fase di esecuzione, nonché come creare proprietà che può essere impostata in fase di progettazione."
ms.topic: article
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: e78b76a531e9f8ea88adca46fc59b2063fce14cc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough---using-custom-controls-with-the-xamarin-designer-for-ios"></a>Procedura dettagliata: utilizzo di controlli personalizzati con la finestra di progettazione di Xamarin per iOS

_Questo articolo fornisce una procedura dettagliata che illustra come creare un controllo personalizzato e utilizzarlo nella finestra di progettazione di Xamarin per iOS. Viene illustrato come creare un controllo disponibile nella casella degli strumenti della finestra di progettazione in modo che può essere trascinamento/rilasciato in una vista. Inoltre, viene illustrato come implementare un controllo in modo corretto rendering in fase di progettazione e in fase di esecuzione, nonché come creare proprietà che può essere impostata in fase di progettazione._

## <a name="requirements"></a>Requisiti

La finestra di progettazione di Xamarin per iOS è disponibile in Visual Studio per Mac e Visual Studio 2015 e 2017 in Windows.

Questa guida si presuppone una certa familiarità con il contenuto incluso nel [Getting Started Guide](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Procedura dettagliata

> [!IMPORTANT]
> A partire da Xamarin.Studio 5.5, il modo in cui vengono creati controlli personalizzati è leggermente diverso per le versioni precedenti. Per creare un controllo personalizzato, sia il `IComponent` interfaccia è obbligatoria (con i metodi di implementazione associata) o la classe può essere annotata con `[DesignTimeVisible(true)]`. Il secondo metodo è utilizzato nell'esempio seguente questa procedura dettagliata.


1. Creare una nuova soluzione dal **iOS > App > singola vista > c#** modello, il nome `ScratchTicket`e continuare con la creazione guidata nuovo progetto:


    [![](ios-designable-controls-walkthrough-images/01new.png "Creare una nuova soluzione")](ios-designable-controls-walkthrough-images/01new.png)


1. Creare un nuovo file di classe vuota denominato `ScratchTicketView`:


    [![](ios-designable-controls-walkthrough-images/02new.png "Creare una nuova classe ScratchTicketView")](ios-designable-controls-walkthrough-images/02new.png)


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


1. Aggiungere il `FillTexture.png`, `FillTexture2.png` e `Monkey.png` file (disponibile [da GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) per il **risorse** cartella.
    
1. Fare doppio clic su di `Main.storyboard` file per aprirlo nella finestra di progettazione:

    
    [![](ios-designable-controls-walkthrough-images/03new.png "Finestra di progettazione iOS")](ios-designable-controls-walkthrough-images/03new.png)



1. Trascinamento della selezione un **visualizzazione immagine** dal **della casella degli strumenti** sulla visualizzazione dello storyboard.

    
    [![](ios-designable-controls-walkthrough-images/04new.png "Visualizzazione di un'immagine aggiunti al layout")](ios-designable-controls-walkthrough-images/04new.png)


1. Selezionare il **visualizzazione immagine** e modificare il relativo **immagine** proprietà `Monkey.png`.

    
    [![](ios-designable-controls-walkthrough-images/05new.png "Impostazione della proprietà immagine della visualizzazione immagine per Monkey.png")](ios-designable-controls-walkthrough-images/05new.png)

    
1. Come si sono usando le classi di dimensioni è necessario limitare la visualizzazione dell'immagine. Fare clic sull'immagine due volte per attivare la modalità di vincolo. Di seguito vincolarlo al centro facendo l'handle di blocco al centro e lo allinea sia verticalmente che orizzontalmente:
    
    
    [![](ios-designable-controls-walkthrough-images/06new.png "Centrare l'immagine")](ios-designable-controls-walkthrough-images/06new.png)

    
1. Per vincolare l'altezza e la larghezza, fare clic sugli handle di dimensioni di blocco (l'handle 'ossa' forma) e selezionare larghezza e altezza, rispettivamente:

    
    [![](ios-designable-controls-walkthrough-images/07new.png "Aggiunta di vincoli")](ios-designable-controls-walkthrough-images/07new.png)


1. Aggiornare il frame in base ai vincoli facendo clic sul pulsante Aggiorna sulla barra degli strumenti:


    [![](ios-designable-controls-walkthrough-images/08new.png "La barra degli strumenti di vincoli")](ios-designable-controls-walkthrough-images/08new.png)


1. Successivamente, compilare il progetto in modo che il **Scratch vista Ticket** verrà visualizzato in **componenti personalizzati** nella casella degli strumenti:

    
    [![](ios-designable-controls-walkthrough-images/09new.png "La casella degli strumenti di componenti personalizzati")](ios-designable-controls-walkthrough-images/09new.png)


1. Trascinare e rilasciare un **Scratch vista Ticket** in modo che venga visualizzato sull'immagine monkey. Modificare gli handle di trascinamento in modo che la visualizzazione di Ticket Scratch copre il monkey completamente, come illustrato di seguito:

    
    [![](ios-designable-controls-walkthrough-images/10new.png "Una vista di Ticket di lavoro tramite la visualizzazione di immagini")](ios-designable-controls-walkthrough-images/10new.png)


1. Limitare la visualizzazione di Ticket di memoria virtuale per la visualizzazione di immagini disegnando un rettangolo di delimitazione per selezionare entrambe le visualizzazioni. Selezionare le opzioni per vincolare il frame di larghezza, altezza, Centra e intermedio e aggiornamento in base a vincoli, come illustrato di seguito:
 
    
    [![](ios-designable-controls-walkthrough-images/11new.png "Centratura e aggiunta di vincoli")](ios-designable-controls-walkthrough-images/11new.png)


1. Eseguire l'applicazione e "scratch off" l'immagine per rivelare il monkey.


 [ ![](ios-designable-controls-walkthrough-images/10-app.png "Eseguire un'app di esempio")](ios-designable-controls-walkthrough-images/10-app.png)

## <a name="adding-design-time-properties"></a>Aggiunta di proprietà in fase di progettazione

La finestra di progettazione include anche il supporto in fase di progettazione per controlli personalizzati di proprietà tipo numerici, enumerazione, string, bool, CGSize, UIColor e UIImage. Per dimostrare, aggiungere una proprietà per il `ScratchTicketView` per impostare l'immagine che tal "caso off."

Aggiungere il codice seguente per la `ScratchTicketView` classe per la proprietà:

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

È inoltre possibile aggiungere un controllo null per il `Draw` (metodo), come illustrato di seguito:

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

Tra cui un `ExportAttribute` e `BrowsableAttribute` con l'argomento impostato su `true` comporta la proprietà viene visualizzata nella finestra di progettazione **proprietà** pannello. Modifica della proprietà a un'altra immagine inclusa con il progetto, ad esempio `FillTexture2.png`, comporta l'aggiornamento del controllo in fase di progettazione, come illustrato di seguito:

 [ ![](ios-designable-controls-walkthrough-images/11-customproperty.png "Modifica le proprietà in fase di progettazione")](ios-designable-controls-walkthrough-images/10-app.png)

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come creare un controllo personalizzato, nonché di usarla in un'applicazione iOS utilizzando la finestra di progettazione iOS. È stato illustrato come creare e compilare il controllo per renderlo disponibile per un'applicazione nella finestra di progettazione **della casella degli strumenti**. Inoltre, è stato illustrato come implementare il controllo in modo che esegue il rendering correttamente in fase di progettazione e runtime, nonché come esporre le proprietà del controllo personalizzato nella finestra di progettazione.



## <a name="related-links"></a>Collegamenti correlati

- [ScratchTicket (esempio)](https://developer.xamarin.com/samples/monotouch/ScratchTicket/)
- [immagini necessarie (esempio)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
