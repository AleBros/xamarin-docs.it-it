---
title: TextKit in xamarin. IOS
description: Questo documento descrive come usare TextKit in xamarin. IOS. TextKit testo potenti funzionalità di layout e rendering.
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f08e37d17cc32e45232d54cc4a51bb48d7ec94b1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111421"
---
# <a name="textkit-in-xamarinios"></a>TextKit in xamarin. IOS

TextKit è una nuova API che offre funzionalità di layout e rendering del testo completa. È basato su framework Core testo a basso livello, ma è molto più facile da usare rispetto al testo di base.

Per rendere le funzionalità di TextKit disponibile per i controlli standard, numerosi controlli di testo iOS sono stati reimplementati usare TextKit, tra cui:

-  UITextView
-  UITextField
-  UILabel

## <a name="architecture"></a>Architettura

TextKit offre un'architettura a più livelli che separa l'archiviazione di testo dal layout e visualizzazione, ad esempio le classi seguenti:

-  `NSTextContainer` : Fornisce la geometria di cui viene usato per il testo di layout e il sistema di coordinate.
-  `NSLayoutManager` – Dispone il testo da trasformare il testo in glifi. 
-  `NSTextStorage` : Contiene i dati di testo, nonché gestisce gli aggiornamenti delle proprietà testo batch. Tutti gli aggiornamenti batch sono passati al gestore di layout per l'elaborazione delle modifiche, ad esempio il ricalcolo del layout e ridisegnando il testo effettivo.


Queste tre classi vengono applicate a una vista che esegue il rendering di testo. Gestione visualizzazioni, come del testo incorporato `UITextView`, `UITextField`, e `UILabel` sono già disponibili impostata, ma è possibile creare e applicarle a qualsiasi `UIView` anche l'istanza.

Nella figura seguente viene illustrata questa architettura:

 ![](textkit-images/textkitarch.png "Questa figura illustra l'architettura TextKit")

## <a name="text-storage-and-attributes"></a>Gli attributi e la memorizzazione del testo

Il `NSTextStorage` la classe contiene il testo visualizzato da una vista. Comunica inoltre le modifiche per il testo, ad esempio modifiche ai caratteri o i relativi attributi: per la gestione di layout per la visualizzazione. `NSTextStorage` eredita da `MSMutableAttributed` stringa, che consente le modifiche agli attributi di testo per specificare in batch tra `BeginEditing` e `EndEditing` chiamate.

Ad esempio, il frammento di codice seguente specifica una modifica in primo piano e sfondo colori, rispettivamente e destinato a determinati intervalli:

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

Dopo aver `EndEditing` viene chiamato, le modifiche vengono inviate al gestore di layout, che a sua volta esegue qualsiasi layout necessarie e i calcoli per il rendering per il testo da visualizzare nella visualizzazione.

## <a name="layout-with-exclusion-path"></a>Layout con percorso di esclusione

TextKit inoltre supporta il layout e consente scenari complessi, ad esempio testo a più colonne e propagazione i percorsi specificati chiamato *esclusione percorsi*. Esclusione percorsi vengono applicati per il contenitore di testo, che modifica la geometria del layout del testo, quindi il testo può circondare i percorsi specificati.

Aggiunta di un percorso di esclusione richiede l'impostazione di `ExclusionPaths` proprietà nel gestore del layout. Impostando questa proprietà, il gestore di layout invalidare il layout del testo e scorrere il testo racchiudere il percorso di esclusione.

### <a name="exclusion-based-on-a-cgpath"></a>Esclusione in base un CGPath

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

Questo codice aggiunge il supporto per il disegno nella visualizzazione di testo tramite Core Graphics. Poiché il `UITextView` classe ora è incorporata per l'uso TextKit per il rendering del testo e il layout, è possibile usare tutte le funzionalità di TextKit, ad esempio l'impostazione di percorsi di esclusione.

> [!IMPORTANT]
> Le sottoclassi in questo esempio `UITextView` aggiungere touch supporto di disegno. Creazione di una sottoclasse `UITextView` non è necessario per ottenere le funzionalità di TextKit.



Dopo che l'utente lo disegna nella visualizzazione di testo, il disegnato `CGPath` viene applicato a un `UIBezierPath` istanza impostando la `UIBezierPath.CGPath` proprietà:

```csharp
bezierPath.CGPath = exclusionPath;
```

Aggiornare la riga di codice seguente esegue il layout del testo aggiornare racchiudere il percorso:

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

Lo screenshot seguente illustra come il layout del testo cambia in modo che scorrano racchiudere il percorso creato:

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")--> 
![](textkit-images/exclusionpath2.png "In questo screenshot illustra come il layout del testo cambia in modo che scorrano intorno al tracciato disegnato")

Si noti che il gestore di layout `AllowsNonContiguousLayout` proprietà è impostata su false, in questo caso. In questo modo il layout di ricalcolo per tutti i casi in cui il testo viene modificato. Se impostato su true possono essere utili le prestazioni evitando un aggiornamento full-layout, soprattutto nel caso dei documenti di grandi dimensioni. Tuttavia, l'impostazione `AllowsNonContiguousLayout` a true potrebbe impedire il percorso di esclusione di aggiornamento del layout in alcune circostanze, ad esempio, se si immette testo in fase di esecuzione senza un ritorno a finali prima il percorso da impostare.


## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a iOS 7 (esempio)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Panoramica dell'interfaccia utente di iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)
