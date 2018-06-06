---
title: TextKit in xamarin. IOS
description: Questo documento descrive come usare TextKit in xamarin. IOS. TextKit fornisce testo potente funzionalità di layout e rendering.
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: ac80d1d07f5649d377dd6fdefcb4911ba9ec2dcb
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788335"
---
# <a name="textkit-in-xamarinios"></a>TextKit in xamarin. IOS

TextKit è una nuova API che contiene testo potente funzionalità di layout e rendering. Si basa su framework Core testo basso livello, ma è molto più semplice da utilizzare rispetto al testo dei componenti di base.

Per rendere le funzionalità di TextKit disponibile per i controlli standard, diversi controlli di testo iOS sono stati nuovamente implementati per utilizzare TextKit, tra cui:

-  UITextView
-  UITextField
-  UILabel

## <a name="architecture"></a>Architettura

TextKit fornisce un'architettura a più livelli che separa lo spazio di archiviazione di testo dal layout e visualizzazione, ad esempio le classi seguenti:

-  `NSTextContainer` : Fornisce la geometria che viene utilizzato per il testo di layout e il sistema di coordinate.
-  `NSLayoutManager` : Definisce il layout di testo, attivando il testo in glifi. 
-  `NSTextStorage` : Contiene i dati di testo, nonché gestisce gli aggiornamenti delle proprietà testo batch. Tutti gli aggiornamenti batch vengono assegnati al gestore di layout per l'elaborazione delle modifiche, ad esempio il ricalcolo del layout e ridisegnando il testo effettivo.


Queste tre classi vengono applicate a una visualizzazione che esegue il rendering di testo. Il testo predefinito Gestione visualizzazioni, ad esempio `UITextView`, `UITextField`, e `UILabel` dispone di tali set, ma è possibile creare e applicare a qualsiasi `UIView` anche l'istanza.

Nella figura seguente viene illustrata questa architettura:

 ![](textkit-images/textkitarch.png "Nella figura viene illustrata l'architettura TextKit")

## <a name="text-storage-and-attributes"></a>Gli attributi e l'archiviazione di testo

La `NSTextStorage` classe contiene il testo visualizzato da una vista. Comunica inoltre le modifiche per il testo, ad esempio modifiche relative ai caratteri o i relativi attributi - per la gestione di layout per la visualizzazione. `NSTextStorage` eredita da `MSMutableAttributed` stringa, sono consentite modifiche agli attributi di testo per specificare in batch tra `BeginEditing` e `EndEditing` chiamate.

Ad esempio, il frammento di codice seguente specifica una modifica in primo piano e sfondo, rispettivamente, colori e destinato a determinati intervalli:

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

Dopo aver `EndEditing` viene chiamato, le modifiche vengono inviate al gestore di layout, che a sua volta esegue qualsiasi layout necessarie e i calcoli di rendering per il testo da visualizzare nella vista.

## <a name="layout-with-exclusion-path"></a>Layout con il percorso di esclusione

TextKit inoltre supporta il layout e consente per scenari complessi, ad esempio testo a più colonne e propagazione testo percorsi specificati chiamato *percorsi di esclusione*. I percorsi di esclusione vengono applicati per il contenitore di testo, che modifica la geometria del layout del testo, causando il testo scorra i percorsi specificati.

Aggiunta di un percorso di esclusione richiede l'impostazione di `ExclusionPaths` proprietà nel gestore del layout. Impostando questa proprietà, il gestore di layout invalidare il layout del testo e scorrere il testo di racchiudere il percorso di esclusione.

### <a name="exclusion-based-on-a-cgpath"></a>Esclusione in base a un CGPath

Tenere presente quanto segue `UITextView` implementazione sottoclasse:

```csharp
public class ExclusionPathView : UITextView
{
    CGPath exclusionPath;
    CGPoint initialPoint;
    CGPoint latestPoint;
    UIBezierPath bezierPath;

    public ExclusionPathView (string text)
    {
        Text = text;
        ContentInset = new UIEdgeInsets (20, 0, 0, 0);
        BackgroundColor = UIColor.White;
        exclusionPath = new CGPath ();
        bezierPath = UIBezierPath.Create ();

        LayoutManager.AllowsNonContiguousLayout = false;
    }

    public override void TouchesBegan (NSSet touches, UIEvent evt)
    {
        base.TouchesBegan (touches, evt);

        var touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt)
    {
        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }

    public override void TouchesEnded (NSSet touches, UIEvent evt)
    {
        base.TouchesEnded (touches, evt);

        bezierPath.CGPath = exclusionPath;
        TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
    }

    public override void Draw (CGRect rect)
    {
        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            using (var g = UIGraphics.GetCurrentContext ()) {

                g.SetLineWidth (4);
                UIColor.Blue.SetStroke ();

                if (exclusionPath.IsEmpty) {
                    exclusionPath.AddLines (new CGPoint[] { initialPoint, latestPoint });
                } else {
                    exclusionPath.AddLineToPoint (latestPoint);
                }

                g.AddPath (exclusionPath);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
}
```

Questo codice aggiunge il supporto per il disegno nella visualizzazione di testo con grafica di base. Poiché il `UITextView` classe è ora compilata per usare TextKit per il rendering del testo e il layout, è possibile usare tutte le funzionalità di TextKit, ad esempio l'impostazione di percorsi di esclusione.

> [!IMPORTANT]
> Le sottoclassi questo esempio `UITextView` per aggiungere il supporto di disegno tocco. Creazione di sottoclassi `UITextView` non è necessario per ottenere le funzionalità di TextKit.



Dopo che l'utente disegna nella visualizzazione di testo, il disegnata `CGPath` viene applicato a un `UIBezierPath` istanza impostando il `UIBezierPath.CGPath` proprietà:

```csharp
bezierPath.CGPath = exclusionPath;
```

Aggiornare la riga seguente di codice rende il layout del testo aggiornare racchiudere il percorso:

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

Nella schermata seguente viene illustrato come il layout del testo cambia per flusso di racchiudere il percorso creato:

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")--> 
![](textkit-images/exclusionpath2.png "Questa schermata viene illustrato come il layout del testo cambia per racchiudere il percorso disegnato del flusso")

Si noti che il gestore di layout `AllowsNonContiguousLayout` proprietà è impostata su false, in questo caso. In questo modo il layout di ricalcolo per tutti i casi in cui il testo viene modificato. Impostando questo valore su true può sfruttare le prestazioni evitando un aggiornamento completo layout, in particolare nel caso di documenti di grandi dimensioni. Tuttavia, l'impostazione `AllowsNonContiguousLayout` a true impedirebbero il percorso di esclusione aggiornare il layout in alcune circostanze, ad esempio, se il testo viene immesso in fase di esecuzione senza un finale ritorno a capo prima il percorso da impostare.


## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a iOS 7 (esempio)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Panoramica dell'interfaccia utente di iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)
