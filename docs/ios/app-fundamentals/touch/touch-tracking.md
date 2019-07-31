---
title: Rilevamento del dito multitocco in Novell. iOS
description: Questo documento descrive come tenere traccia delle singole dita nei movimenti multitocco in un'app Novell. iOS. Si concentra su un esempio di app per il disegno di un dito.
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: cdf6e78356ee1c846b5921957e8eda53931a3c6b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655163"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Rilevamento del dito multitocco in Novell. iOS

_Questo documento illustra come tenere traccia degli eventi di tocco da più dita_

In alcuni casi un'applicazione multitocco deve tenere traccia delle singole dita mentre si spostano simultaneamente sullo schermo. Una tipica applicazione è un programma di disegno con dito. Si vuole che l'utente sia in grado di creare con un solo dito, ma anche di creare con più dita contemporaneamente. Man mano che il programma elabora più eventi Touch, deve distinguere le dita.

Quando un dito tocca per la prima volta lo schermo, [`UITouch`](xref:UIKit.UITouch) iOS crea un oggetto per quel dito. Questo oggetto rimane invariato quando si sposta il dito sullo schermo e quindi si solleva dallo schermo, a quel punto l'oggetto viene eliminato. Per tenere traccia delle dita, un programma dovrebbe evitare di archiviare `UITouch` direttamente questo oggetto. Al contrario, può utilizzare la [`Handle`](xref:Foundation.NSObject.Handle) proprietà di tipo `IntPtr` per identificare in modo univoco `UITouch` questi oggetti.

Quasi sempre, un programma che tiene traccia delle singole dita mantiene un dizionario per il rilevamento del tocco. Per un programma iOS, la chiave del dizionario è `Handle` il valore che identifica un dito particolare. Il valore del dizionario dipende dall'applicazione. Nel programma [FingerPaint](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) , ogni tratto del dito (dal tocco alla versione) viene associato a un oggetto che contiene tutte le informazioni necessarie per eseguire il rendering della linea disegnata con tale dito. Il programma definisce una classe `FingerPaintPolyline` di piccole dimensioni a questo scopo:

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

Ogni polilinea ha un colore, una larghezza del tratto e un oggetto [`CGPath`](xref:CoreGraphics.CGPath) graphics iOS per accumulare ed eseguire il rendering di più punti della linea mentre viene disegnata.


Tutto il resto del codice riportato di seguito è contenuto in una `UIView` derivata denominata `FingerPaintCanvasView`. Tale classe mantiene un dizionario di oggetti di tipo `FingerPaintPolyline` durante il tempo in cui vengono tracciati attivamente da una o più dita:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Questo dizionario consente alla visualizzazione di ottenere rapidamente le `FingerPaintPolyline` informazioni associate a ogni dito in base alla `Handle` proprietà dell' `UITouch` oggetto.

La `FingerPaintCanvasView` classe gestisce anche un `List` oggetto per le polilinee che sono state completate:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Gli oggetti in questo `List` oggetto sono nello stesso ordine in cui sono stati disegnati.

`FingerPaintCanvasView`esegue l'override di cinque `View`metodi definiti da:

- [`TouchesBegan`](xref:UIKit.UIResponder.TouchesBegan(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesMoved`](xref:UIKit.UIResponder.TouchesMoved(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesEnded`](xref:UIKit.UIResponder.TouchesEnded(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesCancelled`](xref:UIKit.UIResponder.TouchesCancelled(Foundation.NSSet,UIKit.UIEvent))
- [`Draw`](xref:UIKit.UIView.Draw(CoreGraphics.CGRect))

Le varie `Touches` sostituzioni accumulano i punti che costituiscono le polilinee.

L'override`Draw`di [] Disegna le polilinee completate e quindi le polilinee in corso:

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

Ognuna delle `Touches` sostituzioni segnala potenzialmente le azioni di più dita, indicate da uno o più `UITouch` oggetti archiviati nell' `touches` argomento al metodo. Le `TouchesBegan` sostituzioni eseguono il ciclo attraverso questi oggetti. Per ogni `UITouch` oggetto, il metodo crea e Inizializza un nuovo `FingerPaintPolyline` oggetto, inclusa l'archiviazione della posizione iniziale del dito ottenuto dal `LocationInView` metodo. Questo `FingerPaintPolyline` oggetto viene aggiunto `InProgressPolylines` al `Handle` dizionario`UITouch` utilizzando la proprietà dell'oggetto come chiave del dizionario:

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

Il metodo termina chiamando `SetNeedsDisplay` per generare una chiamata `Draw` all'override e per aggiornare la schermata.

Quando il dito o le dita `View` si muovono sullo schermo, riceve più chiamate alla relativa `TouchesMoved` sostituzione. Questa sostituzione esegue `UITouch` `touches` in modo analogo il loop negli oggetti archiviati nell'argomento e aggiunge il percorso corrente del dito al percorso grafico:

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

La `touches` raccolta contiene `UITouch` solo gli oggetti per le dita spostate dall'ultima chiamata a o `TouchesMoved`a `TouchesBegan` . Se sono necessari `UITouch` oggetti corrispondenti a *tutte* le dita attualmente in contatto con lo schermo, tali informazioni sono disponibili tramite `AllTouches` `UIEvent` la proprietà dell'argomento al metodo.

L' `TouchesEnded` override è costituito da due processi. Deve aggiungere l'ultimo punto al percorso di grafica e trasferire l' `FingerPaintPolyline` oggetto `inProgressPolylines` dal dizionario all' `completedPolylines` elenco:

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

La `TouchesCancelled` sostituzione viene gestita semplicemente abbandonando l' `FingerPaintPolyline` oggetto nel dizionario:

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

[![](touch-tracking-images/image01.png "Tenere traccia delle singole dita e disegnare i risultati sullo schermo")](touch-tracking-images/image01.png#lightbox)

A questo punto si è appreso come è possibile tenere traccia delle singole dita sullo schermo e distinguerle tra loro.



## <a name="related-links"></a>Collegamenti correlati

- [Guida a Novell Android equivalente](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
