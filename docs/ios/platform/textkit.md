---
title: TextKit in Xamarin.iOS
description: Questo documento descrive come usare TextKit in Xamarin.iOS. TextKit fornisce funzionalità avanzate di rendering e layout del testo.
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 6c1dee464de1f7ba708b1f7d60affc1616e71ee9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031416"
---
# <a name="textkit-in-xamarinios"></a>TextKit in Xamarin.iOS

TextKit è una nuova API che offre funzionalità avanzate per il layout e il rendering del testo. Si basa sul Framework di testo principale di basso livello, ma è molto più semplice da usare rispetto al testo principale.

Per rendere disponibili le funzionalità di TextKit ai controlli standard, diversi controlli testo iOS sono stati riimplementati per l'uso di TextKit, tra cui:

- UITextView
- UITextField
- UILabel

## <a name="architecture"></a>Architettura

TextKit fornisce un'architettura a più livelli che separa l'archiviazione di testo dal layout e dalla visualizzazione, incluse le classi seguenti:

- `NSTextContainer`: fornisce il sistema di coordinate e la geometria usati per il layout del testo.
- `NSLayoutManager`: viene disposto il testo trasformando il testo in glifi.
- `NSTextStorage`: include i dati di testo e gestisce gli aggiornamenti delle proprietà del testo del batch. Eventuali aggiornamenti batch vengono passati a gestione layout per l'effettiva elaborazione delle modifiche, ad esempio il ricalcolo del layout e il ridisegno del testo.

Queste tre classi vengono applicate a una visualizzazione che esegue il rendering del testo. Le visualizzazioni di gestione del testo predefinite, ad esempio `UITextView`, `UITextField`e `UILabel` sono già impostate, ma è possibile crearle e applicarle anche a qualsiasi istanza di `UIView`.

Questa architettura è illustrata nella figura seguente:

 ![](textkit-images/textkitarch.png "This figure illustrates the TextKit architecture")

## <a name="text-storage-and-attributes"></a>Archiviazione di testo e attributi

La classe `NSTextStorage` include il testo visualizzato da una visualizzazione. Comunica anche le modifiche apportate al testo, ad esempio le modifiche apportate ai caratteri o ai rispettivi attributi, al gestore del layout per la visualizzazione. `NSTextStorage` eredita dalla stringa `MSMutableAttributed`, consentendo la specifica delle modifiche apportate agli attributi di testo in batch tra `BeginEditing` e chiamate `EndEditing`.

Il frammento di codice seguente, ad esempio, specifica una modifica ai colori di primo piano e di sfondo, rispettivamente e ha come destinazione intervalli specifici:

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

Dopo la chiamata di `EndEditing`, le modifiche vengono inviate al gestore del layout, che a sua volta esegue tutti i calcoli di layout e rendering necessari per il testo da visualizzare nella visualizzazione.

## <a name="layout-with-exclusion-path"></a>Layout con percorso di esclusione

TextKit supporta anche il layout e consente scenari complessi, ad esempio il testo a più colonne e la propagazione del testo intorno ai percorsi specificati, detti *percorsi di esclusione*. Il percorso di esclusione viene applicato al contenitore di testo, che modifica la geometria del layout del testo, causando il flusso del testo intorno ai percorsi specificati.

Per aggiungere un percorso di esclusione è necessario impostare la proprietà `ExclusionPaths` in Gestione layout. Impostando questa proprietà, il layout del testo viene invalidato dal gestore del layout e il testo viene propagato attorno al percorso di esclusione.

### <a name="exclusion-based-on-a-cgpath"></a>Esclusione basata su un CGPath

Si consideri la seguente `UITextView` implementazione della sottoclasse:

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

Questo codice aggiunge il supporto per il disegno nella visualizzazione di testo usando la grafica principale. Poiché la classe `UITextView` è ora compilata per usare TextKit per il rendering e il layout del testo, può usare tutte le funzionalità di TextKit, ad esempio l'impostazione dei percorsi di esclusione.

> [!IMPORTANT]
> In questo esempio le sottoclassi `UITextView` per aggiungere il supporto per il disegno tocco. La sottoclasse `UITextView` non è necessaria per ottenere le funzionalità di TextKit.

Dopo che l'utente ha disegnato la visualizzazione di testo, l'`CGPath` disegnata viene applicata a un'istanza di `UIBezierPath` impostando la proprietà `UIBezierPath.CGPath`:

```csharp
bezierPath.CGPath = exclusionPath;
```

L'aggiornamento della riga di codice seguente consente di aggiornare il layout del testo intorno al percorso:

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

Lo screenshot seguente illustra il modo in cui il layout del testo cambia per scorrere il tracciato disegnato:

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")-->
![](textkit-images/exclusionpath2.png "This screenshot illustrates how the text layout changes to flow around the drawn path")

Si noti che in questo caso la proprietà `AllowsNonContiguousLayout` di gestione layout è impostata su false. Questo fa sì che il layout venga ricalcolato per tutti i casi in cui il testo viene modificato. L'impostazione di questa opzione su true può trarre vantaggio dalle prestazioni evitando un aggiornamento del layout completo, soprattutto nel caso di documenti di grandi dimensioni. Tuttavia, l'impostazione di `AllowsNonContiguousLayout` su true impedisce al percorso di esclusione di aggiornare il layout in alcune circostanze, ad esempio se il testo viene inserito in fase di esecuzione senza un ritorno a capo finale prima del percorso impostato.

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione ad iOS 7 (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Panoramica dell'interfaccia utente di iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)
