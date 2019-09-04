---
title: Uso di controlli personalizzati con iOS Designer
description: In questo documento viene descritto come creare un controllo personalizzato e usarlo con il Xamarin Designer per iOS. Mostra come rendere disponibile il controllo nella casella degli strumenti della finestra di progettazione di iOS, implementare il controllo in modo che esegua il rendering corretto e la fase di progettazione e altro ancora.
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 65b6f11662cdb8e1814e1146faf70cd01b2c11bc
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227093"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>Uso di controlli personalizzati con iOS Designer

## <a name="requirements"></a>Requisiti

Il Xamarin Designer per iOS è disponibile in Visual Studio per Mac e Visual Studio 2017 e versioni successive in Windows.

Questa guida presuppone una certa familiarità con i contenuti trattati nelle [guide Introduzione](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Procedura dettagliata

> [!IMPORTANT]
> A partire da Novell. Studio 5,5, il modo in cui vengono creati i controlli personalizzati è leggermente diverso rispetto alle versioni precedenti. Per creare un controllo personalizzato, è necessario `IComponent` che l'interfaccia sia obbligatoria (con i metodi di implementazione associati) oppure che la classe possa `[DesignTimeVisible(true)]`essere annotata con. Il secondo metodo viene usato nell'esempio di procedura dettagliata seguente.


1. Creare una nuova soluzione dall' **app iOS > > > C# modello di applicazione di visualizzazione singola** `ScratchTicket`, denominarla e continuare con la creazione guidata nuovo progetto:

    [![](ios-designable-controls-walkthrough-images/01new.png "Crea una nuova soluzione")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. Creare un nuovo file di classe vuoto `ScratchTicketView`denominato:

    [![](ios-designable-controls-walkthrough-images/02new.png "Creare una nuova classe ScratchTicketView")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. Aggiungere il codice seguente per `ScratchTicketView` la classe:

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


1. Aggiungere i `FillTexture.png`file `FillTexture2.png` , `Monkey.png` e (disponibili [da GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) alla cartella resources.

1. Fare doppio clic sul `Main.storyboard` file per aprirlo nella finestra di progettazione:

    [![](ios-designable-controls-walkthrough-images/03new.png "IOS designer")](ios-designable-controls-walkthrough-images/03new.png#lightbox)


1. Trascinare o rilasciare una **visualizzazione immagine** dalla **casella degli strumenti** alla visualizzazione nello storyboard.

    [![](ios-designable-controls-walkthrough-images/04new.png "Visualizzazione immagine aggiunta al layout")](ios-designable-controls-walkthrough-images/04new.png#lightbox)


1. Selezionare la **visualizzazione immagine** e impostare la relativa proprietà Image `Monkey.png`su.

    [![](ios-designable-controls-walkthrough-images/05new.png "Impostazione della proprietà immagine visualizzazione immagine su Monkey. png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)


1. Quando si usano le classi di dimensioni, è necessario vincolare questa visualizzazione immagine. Fare clic due volte sull'immagine per inserirla in modalità vincolo. È possibile vincolarlo al centro facendo clic sul punto di blocco centrale e allinearlo verticalmente e orizzontalmente:

    [![](ios-designable-controls-walkthrough-images/06new.png "Centramento dell'immagine")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. Per vincolare l'altezza e la larghezza, fare clic sugli handle di blocco delle dimensioni (handle a forma di "Bone") e selezionare rispettivamente larghezza e altezza:

    [![](ios-designable-controls-walkthrough-images/07new.png "Aggiunta di vincoli")](ios-designable-controls-walkthrough-images/07new.png#lightbox)


1. Aggiornare il frame in base ai vincoli facendo clic sul pulsante Aggiorna sulla barra degli strumenti:

    [![](ios-designable-controls-walkthrough-images/08new.png "Barra degli strumenti vincoli")](ios-designable-controls-walkthrough-images/08new.png#lightbox)


1. Successivamente, compilare il progetto in modo che la **visualizzazione del ticket Scratch** venga visualizzata in **componenti personalizzati** nella casella degli strumenti:

    [![](ios-designable-controls-walkthrough-images/09new.png "Casella degli strumenti componenti personalizzati")](ios-designable-controls-walkthrough-images/09new.png#lightbox)


1. Trascinare e rilasciare una **visualizzazione del ticket Scratch** in modo che venga visualizzata sull'immagine scimmia. Modificare i quadratini di trascinamento in modo che la visualizzazione del ticket Scratch copra completamente la scimmia, come illustrato di seguito:

    [![](ios-designable-controls-walkthrough-images/10new.png "Visualizzazione del ticket Scratch sulla visualizzazione immagine")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. Vincolare la visualizzazione del ticket Scratch alla visualizzazione immagine disegnando un rettangolo di delimitazione per selezionare entrambe le visualizzazioni. Selezionare le opzioni per vincolarla ai frame larghezza, altezza, centrale e centrale e aggiorna in base ai vincoli, come illustrato di seguito:

    [![](ios-designable-controls-walkthrough-images/11new.png "Centramento e aggiunta di vincoli")](ios-designable-controls-walkthrough-images/11new.png#lightbox)


1. Eseguire l'applicazione e "Scratch" nell'immagine per rivelare la scimmia.

    [![](ios-designable-controls-walkthrough-images/10-app.png "Esecuzione di un'app di esempio")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>Aggiunta di proprietà in fase di progettazione

La finestra di progettazione include anche il supporto in fase di progettazione per i controlli personalizzati del tipo di proprietà numeric, Enumeration, String, bool, CGSize, UIColor e UIImage. Per illustrare, aggiungere una proprietà a `ScratchTicketView` per impostare l'immagine "graffiata".

Aggiungere il codice seguente alla `ScratchTicketView` classe per la proprietà:

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

Potrebbe anche essere necessario aggiungere un controllo null al `Draw` metodo, come indicato di seguito:

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

L'inclusione di `BrowsableAttribute` `true` e con l'argomento impostato su comporta la visualizzazione della proprietà nel pannello Proprietà della finestra di progettazione. `ExportAttribute` La modifica della proprietà in un'altra immagine inclusa nel progetto, ad `FillTexture2.png`esempio, comporta l'aggiornamento del controllo in fase di progettazione, come illustrato di seguito:

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "Modifica delle proprietà della fase di progettazione")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come creare un controllo personalizzato e come utilizzarlo in un'applicazione iOS tramite iOS designer. Abbiamo visto come creare e compilare il controllo per renderlo disponibile a un'applicazione nella **casella degli strumenti**della finestra di progettazione. È stato inoltre illustrato come implementare il controllo in modo che venga eseguito correttamente in fase di progettazione e in fase di esecuzione, nonché come esporre le proprietà di un controllo personalizzato nella finestra di progettazione.



## <a name="related-links"></a>Collegamenti correlati

- [ScratchTicket (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/scratchticket)
- [immagini obbligatorie (esempio)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
