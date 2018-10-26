---
title: Con un dito multitocco di rilevamento in xamarin. Android
description: In questo argomento viene illustrato come tenere traccia degli eventi di tocco da più dita
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 34a9d2d9b8acb05a1b978a70e85038507032faaa
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104687"
---
# <a name="multi-touch-finger-tracking"></a>Rilevamento di un dito multitocco

_In questo argomento viene illustrato come tenere traccia degli eventi di tocco da più dita_

Vi sono casi quando è necessario che un'applicazione multi-touch per tenere traccia delle singole dita durante lo spostamento contemporaneamente sullo schermo. Una tipica applicazione è un programma finger-paint. Si vuole che l'utente sia in grado di disegnare con un singolo dito, ma anche da disegnare con più dita in una sola volta. Mentre il programma elabora più eventi di tocco, è necessario distinguere gli eventi che corrispondono a ogni dito. Android fornisce un codice ID per questo scopo, ma come ottenere e la gestione di tale codice può essere un po' difficoltoso.

Per tutti gli eventi associati con un dito particolare, il codice ID rimane invariato. Il codice ID viene assegnato quando un dito prima di tutto tocca lo schermo e non è più valido dopo solleva il dito dallo schermo.
Questi codici ID sono numeri interi generalmente di dimensioni molto ridotte e li riutilizza Android per gli eventi di tocco più avanti.

Quasi sempre un programma che tiene traccia delle singole dita gestisce un dizionario per il tocco di rilevamento. La chiave del dizionario è il codice ID che identifica un dito particolare. Il valore del dizionario dipende dall'applicazione. Nel [Pittura con le dita](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programma, tratto ogni dito (dal tocco alla versione) è associato a un oggetto che contiene tutte le informazioni necessarie per eseguire il rendering la riga disegnata con il dito. Il programma definisce un piccolo `FingerPaintPolyline` classe per questo scopo:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new Path();
    }

    public Color Color { set; get; }

    public float StrokeWidth { set; get; }

    public Path Path { private set; get; }
}
```

Ciascuna polilinea dispone di un colore, uno spessore e una grafica di Android [ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/) oggetto accumulare ed eseguire il rendering di più punti della riga che viene viene tracciato.

Nella parte restante di codice riportato di seguito è contenuta un `View` derivative denominato `FingerPaintCanvasView`. Che classe gestisce un dizionario di oggetti di tipo `FingerPaintPolyline` durante l'ora in cui sono attivamente da disegnare da uno o più dita:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Questo dizionario consente la visualizzazione ottenere rapidamente la `FingerPaintPolyline` informazioni associate a un particolare dito.

Il `FingerPaintCanvasView` classe gestisce anche un `List` oggetto per le polilinee che sono state completate:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Gli oggetti in questo `List` sono nello stesso ordine che sono stati disegnati.

`FingerPaintCanvasView` esegue l'override di due metodi definiti da `View`: [`OnDraw`](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/)
e [ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/).
Nel relativo `OnDraw` override, la visualizzazione disegna le polilinee completate e quindi disegna le polilinee in corso.

L'override del `OnTouchEvent` metodo inizia ottenendo un `pointerIndex` valore il `ActionIndex` proprietà. Ciò `ActionIndex` valore consente di distinguere tra più dita, ma non è coerenza tra più eventi. Per questo motivo, si utilizza il `pointerIndex` per ottenere il puntatore `id` valore il `GetPointerId` (metodo). Questo ID *è* coerente tra più eventi:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        // ...
    }

    // Invalidate to update the view
    Invalidate();

    // Request continued touch input
    return true;
}
```

Si noti che la sostituzione viene utilizzato il `ActionMasked` proprietà nel `switch` istruzione anziché il `Action` proprietà. La ragione di questo comportamento è la seguente:

Quando ha a che fare con multi-touch, il `Action` un valore della proprietà è `MotionEventsAction.Down` per il primo dito toccare la schermata e quindi i valori di `Pointer2Down` e `Pointer3Down` come le dita seconda e terza anche toccano lo schermo. Come rendere le dita quarta e quinta contatto, il `Action` proprietà presenta valori numerici che non corrispondono ancora ai membri del `MotionEventsAction` enumerazione! È necessario esaminare i valori dei flag di bit i valori per interpretare il relativo significato.

In modo analogo, come le dita lasciare contatto con la schermata, il `Action` ha i valori della proprietà `Pointer2Up` e `Pointer3Up` per le dita seconda e terza, e `Up` per il dito prima.

Il `ActionMasked` proprietà assume un minore numero di valori poiché è destinato a essere usato in combinazione con il `ActionIndex` proprietà distinguere tra più dita. Quando le dita toccano lo schermo, la proprietà può essere solo uguale `MotionEventActions.Down` per il primo dito e `PointerDown` per dita successive. Perché le dita lasciare la schermata `ActionMasked` presenta i valori `Pointer1Up` per le successive DITA e `Up` per il dito prima.

Quando si usa `ActionMasked`, il `ActionIndex` consente di distinguere tra le dita successivi per il tocco e lasciare la schermata, ma è in genere non è necessario utilizzare tale valore, ad eccezione come argomento agli altri metodi nel `MotionEvent` oggetto. Per Multi-touch, uno dei più importanti di questi metodi è `GetPointerId` chiamato nel codice precedente. Metodo restituito un valore che è possibile usare per una chiave del dizionario per associare eventi specifici per le dita.

Il `OnTouchEvent` esegue l'override nel [Pittura con le dita](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programmare i processi il `MotionEventActions.Down` e `PointerDown` degli eventi in modo identico tramite la creazione di un nuovo `FingerPaintPolyline` oggetto e l'aggiunta al dizionario:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:

            // Create a Polyline, set the initial point, and store it
            FingerPaintPolyline polyline = new FingerPaintPolyline
            {
                Color = StrokeColor,
                StrokeWidth = StrokeWidth
            };

            polyline.Path.MoveTo(args.GetX(pointerIndex),
                                 args.GetY(pointerIndex));

            inProgressPolylines.Add(id, polyline);
            break;
        // ...
    }
    // ...        
}
```

Si noti che il `pointerIndex` viene inoltre utilizzato per ottenere la posizione del dito all'interno della visualizzazione. Tutte le informazioni di tocco sono associate il `pointerIndex` valore. Il `id` identifica in modo univoco le dita tra più messaggi, in modo che del utilizzato per creare la voce del dizionario.

Allo stesso modo, il `OnTouchEvent` eseguire l'override anche gli handle il `MotionEventActions.Up` e `Pointer1Up` in modo identico trasferendo la polilinea completata per il `completedPolylines` raccolta in modo che possono essere disegnate durante il `OnDraw` eseguire l'override. Il codice rimuove anche il `id` voce dal dizionario:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Up:
        case MotionEventActions.Pointer1Up:

            inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                args.GetY(pointerIndex));

            // Transfer the in-progress polyline to a completed polyline
            completedPolylines.Add(inProgressPolylines[id]);
            inProgressPolylines.Remove(id);
            break;

        case MotionEventActions.Cancel:
            inProgressPolylines.Remove(id);
            break;
    }
    // ...        
}
```

Ora per la parte più complicata.

Tra la freccia giù e gli eventi, in genere sono presenti molti `MotionEventActions.Move` gli eventi. Questi vengono aggregati in una singola chiamata a `OnTouchEvent`, e devono essere gestiti in modo diverso dal `Down` e `Up` eventi. Il `pointerIndex` ottenuto in precedenza dal valore di `ActionIndex` proprietà deve essere ignorata. Invece il metodo deve ottenere più `pointerIndex` i valori dal ciclo compreso tra 0 e il `PointerCount` proprietà, quindi ottenere un `id` per ognuno di questi `pointerIndex` valori:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Move:

            // Multiple Move events are bundled, so handle them differently
            for (pointerIndex = 0; pointerIndex < args.PointerCount; pointerIndex++)
            {
                id = args.GetPointerId(pointerIndex);

                inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                    args.GetY(pointerIndex));
            }
            break;
        // ...
    }
    // ...        
}
```

Questo tipo di elaborazione consente la [Pittura con le dita](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programma per tenere traccia delle singole DITA e tracciare i risultati sullo schermo:

[![Screenshot di esempio dall'esempio Pittura con le dita](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Abbiamo visto come è possibile tenere traccia delle singole dita sullo schermo e distinguere tra di essi.


## <a name="related-links"></a>Collegamenti correlati

- [Guida equivalente Xamarin iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Pittura con le dita (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
