---
title: Rilevamento di multi-Touch dito
description: Questo documento viene illustrato come tenere traccia degli eventi tocco da più dita
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 178bd0a68166f033293b2d807fea449355c1c584
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="multi-touch-finger-tracking"></a>Rilevamento di multi-Touch dito

_Questo documento viene illustrato come tenere traccia degli eventi tocco da più dita_

Vi sono casi quando un'applicazione multi-touch deve tenere traccia di singoli dita durante lo spostamento contemporaneamente sullo schermo. Una tipica applicazione è un programma finger-paint. Si desidera che l'utente sia in grado di disegnare con un singolo dito, ma anche per disegnare con le dita più in una sola volta. Mentre il programma elabora più eventi tocco, è necessario distinguere tra queste dita.

Quando un dito tocca innanzitutto lo schermo, iOS viene creato un [ `UITouch` ](https://developer.xamarin.com/api/type/UIKit.UITouch/) oggetto per il dito. L'oggetto rimane lo stesso come il dito sullo schermo e quindi solleva dalla schermata, a quel punto l'oggetto viene eliminato. Per tenere traccia delle dita, un programma deve evitare di archiviare questo `UITouch` direttamente l'oggetto. In alternativa, è possibile utilizzare il [ `Handle` ](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) proprietà di tipo `IntPtr` in modo da identificare questi `UITouch` oggetti.

Quasi sempre, un programma che tiene traccia delle dita singoli mantiene un dizionario per il tocco di rilevamento. Per un programma di iOS, la chiave del dizionario è di `Handle` valore che identifica un dito particolare. Il valore del dizionario dipende dall'applicazione. Nel [Pittura con le dita](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) programma, ogni tratto dito (da tocco per rilasciare) è associata a un oggetto che contiene tutte le informazioni necessarie per il rendering della linea disegnata con il dito. Il programma definisce una piccola `FingerPaintPolyline` classe per questo scopo:

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

Ogni polilinea dispone di un colore, uno spessore e una grafica iOS [ `CGPath` ](https://developer.xamarin.com/api/type/CoreGraphics.CGPath/) oggetto vengono accumulate ed eseguire il rendering di più punti della riga come viene disegnata.


Tutto il resto del codice riportato di seguito è contenuto in un `UIView` derivato denominato `FingerPaintCanvasView`. Che classe gestisce un dizionario di oggetti di tipo `FingerPaintPolyline` durante la fase che vengono attivamente da disegnare da uno o più dita:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Questo dizionario consente di ottenere rapidamente la `FingerPaintPolyline` informazioni associate a ogni dito dipendono il `Handle` proprietà del `UITouch` oggetto.

Il `FingerPaintCanvasView` mantiene anche la classe un `List` oggetto per la polilinea che è stata completata:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Gli oggetti in questo `List` sono nello stesso ordine che sono stati disegnati.

`FingerPaintCanvasView` esegue l'override di cinque metodi definiti dal `View`:

- [`TouchesBegan`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesBegan/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesMoved`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesMoved/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesEnded`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesEnded/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesCancelled`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesCancelled/p/Foundation.NSSet/UIKit.UIEvent/)
- [`Draw`](https://developer.xamarin.com/api/member/UIKit.UIView.Draw/p/CoreGraphics.CGRect/)

I vari `Touches` sostituzioni accumulano i punti che compongono la polilinea.

Il [`Draw`] override Disegna la polilinea completata e quindi la polilinea in corso:

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

Ogni il `Touches` sostituzioni potenzialmente segnala le azioni di più dita, indicate da uno o più `UITouch` gli oggetti archiviati nel `touches` argomento del metodo. Il `TouchesBegan` ciclo sostituzioni in questi oggetti. Per ogni `UITouch` dell'oggetto, il metodo crea e Inizializza un nuovo `FingerPaintPolyline` oggetto, tra cui archiviare il percorso iniziale del dito ottenuto il `LocationInView` (metodo). Questo `FingerPaintPolyline` oggetto viene aggiunto al `InProgressPolylines` dizionario utilizzando il `Handle` proprietà del `UITouch` oggetto come chiave di dizionario:

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

Il metodo termina chiamando `SetNeedsDisplay` per generare una chiamata al `Draw` eseguire l'override e per aggiornare la schermata.

Il dito o dita sullo schermo, lo spostamento di `View` ottiene più chiamate al relativo `TouchesMoved` eseguire l'override. Questo override scorre in modo analogo la `UITouch` gli oggetti archiviati nel `touches` argomento e aggiunge il percorso corrente del dito per il percorso grafico:

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

Il `touches` insieme contiene solo le `UITouch` oggetti per le dita che sono stati spostati dopo l'ultima chiamata a `TouchesBegan` o `TouchesMoved`. Se avrai bisogno di `UITouch` gli oggetti corrispondenti alle *tutti* dita attualmente in contatto con la schermata, tali informazioni sono disponibili tramite il `AllTouches` proprietà del `UIEvent` argomento del metodo.

Il `TouchesEnded` override dispone di due processi. L'ultimo punto deve aggiungere al percorso di grafica e al trasferimento il `FingerPaintPolyline` dell'oggetto dal `inProgressPolylines` dizionario utilizzato per il `completedPolylines` elenco:

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

Il `TouchesCancelled` override viene gestita da abbandono semplicemente il `FingerPaintPolyline` oggetto nel dizionario:

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

Consente di tale elaborazione, il [Pittura con le dita](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) programma per tenere traccia delle dita singoli e tracciare i risultati sullo schermo:

[![](touch-tracking-images/image01.png "Rilevamento dita singole e i risultati di disegno sullo schermo")](touch-tracking-images/image01.png#lightbox)

Abbiamo visto come è possibile tenere traccia delle singole dita sullo schermo e distinguere tra di essi.



## <a name="related-links"></a>Collegamenti correlati

- [Equivalente Guida Android di Xamarin](~/android/app-fundamentals/touch/touch-tracking.md)
- [Pittura con le dita (esempio)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
