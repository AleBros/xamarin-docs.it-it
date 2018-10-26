---
title: Con un dito multitocco di rilevamento in xamarin. IOS
description: Questo documento viene descritto come tenere traccia delle singole dita in movimenti multitocco in un'app xamarin. IOS. Coinvolge un esempio di app dipingere.
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a1ddcda84d51b5a8a9220558ddaf9476a2321ee8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105051"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Con un dito multitocco di rilevamento in xamarin. IOS

_Questo documento illustra come tenere traccia degli eventi di tocco da più dita_

Vi sono casi quando è necessario che un'applicazione multi-touch per tenere traccia delle singole dita durante lo spostamento contemporaneamente sullo schermo. Una tipica applicazione è un programma finger-paint. Si vuole che l'utente sia in grado di disegnare con un singolo dito, ma anche da disegnare con più dita in una sola volta. Mentre il programma elabora più eventi di tocco, è necessario distinguere tra le dita.

Quando un dito tocca prima di tutto lo schermo, iOS viene creata una [ `UITouch` ](https://developer.xamarin.com/api/type/UIKit.UITouch/) oggetto per il dito. Questo oggetto rimane invariato il dito sullo schermo e quindi solleva dalla schermata, a quel punto l'oggetto è stato eliminato. Per tenere traccia delle dita, un programma deve evitare di archiviare questo `UITouch` direttamente l'oggetto. In alternativa, è possibile usare la [ `Handle` ](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) vlastnosti typu `IntPtr` in modo da identificare questi `UITouch` oggetti.

Quasi sempre un programma che tiene traccia delle singole dita gestisce un dizionario per il tocco di rilevamento. Per un programma iOS, è la chiave del dizionario di `Handle` valore che identifica un dito particolare. Il valore del dizionario dipende dall'applicazione. Nel [Pittura con le dita](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) programma, tratto ogni dito (dal tocco alla versione) è associato a un oggetto che contiene tutte le informazioni necessarie per eseguire il rendering la riga disegnata con il dito. Il programma definisce un piccolo `FingerPaintPolyline` classe per questo scopo:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new CGPath();
    }

    public CGColor Color { set; get; }

    public float StrokeWidth { set; get; }

    public CGPath Path { private set; get; }
}
```

Ciascuna polilinea dispone di un colore, uno spessore e una grafica di iOS [ `CGPath` ](https://developer.xamarin.com/api/type/CoreGraphics.CGPath/) oggetto accumulare ed eseguire il rendering di più punti della riga che viene viene tracciato.


Tutto il resto del codice riportato di seguito è contenuto un `UIView` derivative denominato `FingerPaintCanvasView`. Che classe gestisce un dizionario di oggetti di tipo `FingerPaintPolyline` durante l'ora in cui sono attivamente da disegnare da uno o più dita:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Questo dizionario consente la visualizzazione ottenere rapidamente la `FingerPaintPolyline` le informazioni associate a ogni dito basano sul `Handle` proprietà del `UITouch` oggetto.

Il `FingerPaintCanvasView` classe gestisce anche un `List` oggetto per le polilinee che sono state completate:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Gli oggetti in questo `List` sono nello stesso ordine che sono stati disegnati.

`FingerPaintCanvasView` esegue l'override di cinque metodi definiti da `View`:

- [`TouchesBegan`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesBegan/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesMoved`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesMoved/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesEnded`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesEnded/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesCancelled`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesCancelled/p/Foundation.NSSet/UIKit.UIEvent/)
- [`Draw`](https://developer.xamarin.com/api/member/UIKit.UIView.Draw/p/CoreGraphics.CGRect/)

I vari `Touches` sostituzioni accumulano i punti che compongono le polilinee.

Il [`Draw`] sostituzione consente di tracciare le polilinee completate e quindi le polilinee in corso:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        // Stroke settings
        context.SetLineCap(CGLineCap.Round);
        context.SetLineJoin(CGLineJoin.Round);

        // Draw the completed polylines
        foreach (FingerPaintPolyline polyline in completedPolylines)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }

        // Draw the in-progress polylines
        foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Ognuna delle `Touches` sostituzioni potenzialmente segnala le azioni di più dita, indicate da uno o più `UITouch` gli oggetti archiviati nel `touches` argomento del metodo. Il `TouchesBegan` ciclo esegue l'override tramite questi oggetti. Per ognuno `UITouch` dell'oggetto, il metodo crea e Inizializza una nuova `FingerPaintPolyline` oggetti, tra cui archiviare la posizione iniziale del dito ottenuto dal `LocationInView` (metodo). Ciò `FingerPaintPolyline` oggetto viene aggiunto al `InProgressPolylines` dizionario tramite il `Handle` proprietà del `UITouch` oggetto come una chiave del dizionario:

```csharp
public override void TouchesBegan(NSSet touches, UIEvent evt)
{
    base.TouchesBegan(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Create a FingerPaintPolyline, set the initial point, and store it
        FingerPaintPolyline polyline = new FingerPaintPolyline
        {
            Color = StrokeColor,
            StrokeWidth = StrokeWidth,
        };

        polyline.Path.MoveToPoint(touch.LocationInView(this));
        inProgressPolylines.Add(touch.Handle, polyline);
    }
    SetNeedsDisplay();
}
```

Il metodo conclude chiamando `SetNeedsDisplay` per generare una chiamata al `Draw` eseguire l'override e per aggiornare la schermata.

Lo spostamento di un dito o dita sullo schermo, il `View` ottiene più chiamate al relativo `TouchesMoved` eseguire l'override. Questa sostituzione consente allo stesso modo di scorrere le `UITouch` gli oggetti archiviati nel `touches` argomento e aggiunge il percorso corrente del dito sul percorso della grafica:

```csharp
public override void TouchesMoved(NSSet touches, UIEvent evt)
{
    base.TouchesMoved(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Add point to path
        inProgressPolylines[touch.Handle].Path.AddLineToPoint(touch.LocationInView(this));
    }
    SetNeedsDisplay();
}
```

Il `touches` raccolta contiene solo quelli `UITouch` gli oggetti per le dita che sono stati spostati dall'ultima chiamata a `TouchesBegan` o `TouchesMoved`. Se avrai bisogno `UITouch` oggetti corrispondenti ai *tutte le* le dita attualmente in contatto con la schermata, tali informazioni sono disponibili tramite il `AllTouches` proprietà del `UIEvent` argomento del metodo.

Il `TouchesEnded` override ha due compiti. L'ultimo punto deve aggiungere al percorso degli elementi grafici e trasferimento la `FingerPaintPolyline` dall'oggetto di `inProgressPolylines` dizionario utilizzato per il `completedPolylines` elenco:

```csharp
public override void TouchesEnded(NSSet touches, UIEvent evt)
{
    base.TouchesEnded(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Get polyline from dictionary and remove it from dictionary
        FingerPaintPolyline polyline = inProgressPolylines[touch.Handle];
        inProgressPolylines.Remove(touch.Handle);

        // Add final point to path and save with completed polylines
        polyline.Path.AddLineToPoint(touch.LocationInView(this));
        completedPolylines.Add(polyline);
    }
    SetNeedsDisplay();
}
```

Il `TouchesCancelled` sostituzione viene gestita da semplicemente venga abbandonato il `FingerPaintPolyline` oggetto nel dizionario:

```csharp
public override void TouchesCancelled(NSSet touches, UIEvent evt)
{
    base.TouchesCancelled(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        inProgressPolylines.Remove(touch.Handle);
    }
    SetNeedsDisplay();
}
```

Consente di questo tipo di elaborazione completamente, il [Pittura con le dita](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) programma per tenere traccia delle singole DITA e tracciare i risultati sullo schermo:

[![](touch-tracking-images/image01.png "Rilevamento delle singole DITA e i risultati di disegno sullo schermo")](touch-tracking-images/image01.png#lightbox)

Abbiamo visto come è possibile tenere traccia delle singole dita sullo schermo e distinguere tra di essi.



## <a name="related-links"></a>Collegamenti correlati

- [Guida di Xamarin Android equivalente](~/android/app-fundamentals/touch/touch-tracking.md)
- [Pittura con le dita (esempio)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
