---
title: Rilevamento del dito multitocco in Novell. iOS
description: Questo documento descrive come tenere traccia delle singole dita nei movimenti multitocco in un'app Novell. iOS. Si concentra su un esempio di app per il disegno di un dito.
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: c3998424c8f4e9482a41e2891e65f0d13d8ac2f3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009183"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Rilevamento del dito multitocco in Novell. iOS

_Questo documento illustra come tenere traccia degli eventi di tocco da più dita_

In alcuni casi un'applicazione multitocco deve tenere traccia delle singole dita mentre si spostano simultaneamente sullo schermo. Una tipica applicazione è un programma di disegno con dito. Si vuole che l'utente sia in grado di creare con un solo dito, ma anche di creare con più dita contemporaneamente. Man mano che il programma elabora più eventi Touch, deve distinguere le dita.

Quando un dito tocca la schermata, iOS crea un oggetto [`UITouch`](xref:UIKit.UITouch) per quel dito. Questo oggetto rimane invariato quando si sposta il dito sullo schermo e quindi si solleva dallo schermo, a quel punto l'oggetto viene eliminato. Per tenere traccia delle dita, un programma dovrebbe evitare di archiviare direttamente questo oggetto `UITouch`. Al contrario, può utilizzare la proprietà [`Handle`](xref:Foundation.NSObject.Handle) di tipo `IntPtr` per identificare in modo univoco questi oggetti di `UITouch`.

Quasi sempre, un programma che tiene traccia delle singole dita mantiene un dizionario per il rilevamento del tocco. Per un programma iOS, la chiave del dizionario è il valore `Handle` che identifica un dito particolare. Il valore del dizionario dipende dall'applicazione. Nel programma [FingerPaint](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) , ogni tratto del dito (dal tocco alla versione) viene associato a un oggetto che contiene tutte le informazioni necessarie per eseguire il rendering della linea disegnata con tale dito. Il programma definisce una classe di `FingerPaintPolyline` ridotta a questo scopo:

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

Ogni polilinea ha un colore, una larghezza del tratto e un oggetto grafico iOS [`CGPath`](xref:CoreGraphics.CGPath) oggetto per accumulare ed eseguire il rendering di più punti della linea mentre viene disegnata.

Tutto il resto del codice riportato di seguito è contenuto in un `UIView` derivato denominato `FingerPaintCanvasView`. Tale classe mantiene un dizionario di oggetti di tipo `FingerPaintPolyline` nel momento in cui vengono attivamente disegnati da una o più dita:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Questo dizionario consente alla visualizzazione di ottenere rapidamente le informazioni `FingerPaintPolyline` associate a ogni dito in base alla proprietà `Handle` dell'oggetto `UITouch`.

La classe `FingerPaintCanvasView` gestisce anche un oggetto `List` per le polilinee completate:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Gli oggetti in questo `List` sono nello stesso ordine in cui sono stati disegnati.

`FingerPaintCanvasView` esegue l'override di cinque metodi definiti da `View`:

- [`TouchesBegan`](xref:UIKit.UIResponder.TouchesBegan(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesMoved`](xref:UIKit.UIResponder.TouchesMoved(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesEnded`](xref:UIKit.UIResponder.TouchesEnded(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesCancelled`](xref:UIKit.UIResponder.TouchesCancelled(Foundation.NSSet,UIKit.UIEvent))
- [`Draw`](xref:UIKit.UIView.Draw(CoreGraphics.CGRect))

Le varie sostituzioni di `Touches` accumulano i punti che costituiscono le polilinee.

L'override di [`Draw`] Disegna le polilinee completate e quindi le polilinee in corso:

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

Ogni override di `Touches` segnala potenzialmente le azioni di più dita, indicate da uno o più oggetti `UITouch` archiviati nell'argomento `touches` al metodo. Il `TouchesBegan` esegue l'override del ciclo attraverso questi oggetti. Per ogni oggetto `UITouch`, il metodo crea e Inizializza un nuovo oggetto `FingerPaintPolyline`, inclusa l'archiviazione della posizione iniziale del dito ottenuto dal metodo `LocationInView`. Questo `FingerPaintPolyline` oggetto viene aggiunto al dizionario `InProgressPolylines` utilizzando la proprietà `Handle` dell'oggetto `UITouch` come chiave del dizionario:

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

Il metodo termina chiamando `SetNeedsDisplay` per generare una chiamata al `Draw` override e per aggiornare la schermata.

Quando il dito o le dita si muovono sullo schermo, il `View` ottiene più chiamate al relativo `TouchesMoved` override. Questa sostituzione esegue in modo analogo il ciclo attraverso gli oggetti `UITouch` archiviati nell'argomento `touches` e aggiunge il percorso corrente del dito al percorso grafico:

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

La raccolta di `touches` contiene solo gli oggetti `UITouch` per le dita spostate dall'ultima chiamata a `TouchesBegan` o `TouchesMoved`. Se è necessario `UITouch` oggetti corrispondenti a *tutte* le dita attualmente in contatto con lo schermo, tali informazioni sono disponibili tramite la proprietà `AllTouches` dell'argomento `UIEvent` al metodo.

L'override del `TouchesEnded` ha due processi. Deve aggiungere l'ultimo punto al percorso di grafica e trasferire l'oggetto `FingerPaintPolyline` dal dizionario `inProgressPolylines` all'elenco `completedPolylines`:

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

L'override del `TouchesCancelled` viene gestito semplicemente abbandonando l'oggetto `FingerPaintPolyline` nel dizionario:

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

Nel complesso, questa elaborazione consente al programma [FingerPaint](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) di tenere traccia delle singole dita e di tracciare i risultati sullo schermo:

[![](touch-tracking-images/image01.png "Tracking individual fingers and drawing the results on the screen")](touch-tracking-images/image01.png#lightbox)

A questo punto si è appreso come è possibile tenere traccia delle singole dita sullo schermo e distinguerle tra loro.

## <a name="related-links"></a>Collegamenti correlati

- [Guida a Novell Android equivalente](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
