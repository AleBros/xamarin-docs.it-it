---
title: Rilevamento di multi-Touch dito
description: "In questo argomento viene illustrato come tenere traccia degli eventi tocco da più dita"
ms.topic: article
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 53e972211ce506b6bf32ee4785c853982528d92e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="multi-touch-finger-tracking"></a>Rilevamento di multi-Touch dito

_In questo argomento viene illustrato come tenere traccia degli eventi tocco da più dita_

Vi sono casi quando un'applicazione multi-touch deve tenere traccia di singoli dita durante lo spostamento contemporaneamente sullo schermo. Una tipica applicazione è un programma finger-paint. Si desidera che l'utente sia in grado di disegnare con un singolo dito, ma anche per disegnare con le dita più in una sola volta. Mentre il programma elabora più eventi tocco, è necessario distinguere gli eventi che corrispondono a ogni dito. Android fornisce un codice ID per questo scopo, ma può essere un po' complesso recupero e la gestione di tale codice.

Per tutti gli eventi associati a un dito particolare, il codice ID rimane invariato. Il codice ID viene assegnato quando un dito prima tocca lo schermo e diventa non valido dopo solleva il dito dallo schermo.
Questi codici ID sono generalmente di dimensioni molto ridotte numeri interi e li riutilizza Android per gli eventi tocco successivi.

Quasi sempre, un programma che tiene traccia delle dita singoli mantiene un dizionario per il tocco di rilevamento. La chiave del dizionario è il codice di ID che identifica un dito particolare. Il valore del dizionario dipende dall'applicazione. Nel [Pittura con le dita](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programma, ogni tratto dito (da tocco per rilasciare) è associata a un oggetto che contiene tutte le informazioni necessarie per il rendering della linea disegnata con il dito. Il programma definisce una piccola `FingerPaintPolyline` classe per questo scopo:

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

Ogni polilinea dispone di un colore, uno spessore e una grafica Android [ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/) oggetto vengono accumulate ed eseguire il rendering di più punti della riga come viene disegnata.

Nella parte restante di codice riportato di seguito è contenuta in un `View` derivato denominato `FingerPaintCanvasView`. Che classe gestisce un dizionario di oggetti di tipo `FingerPaintPolyline` durante la fase che vengono attivamente da disegnare da uno o più dita:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Questo dizionario consente di ottenere rapidamente la `FingerPaintPolyline` informazioni associate a un particolare dito.

Il `FingerPaintCanvasView` mantiene anche la classe un `List` oggetto per la polilinea che è stata completata:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Gli oggetti in questo `List` sono nello stesso ordine che sono stati disegnati.

`FingerPaintCanvasView` esegue l'override di due metodi definiti dal `View`: [ `OnDraw` ](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/) e [ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/).
Nel relativo `OnDraw` override, la vista Disegna la polilinea completata e quindi disegna la polilinea in corso.

L'override del `OnTouchEvent` metodo inizia recuperando un `pointerIndex` valore il `ActionIndex` proprietà. Questo `ActionIndex` valore distingue più dita, ma non è coerenza tra più eventi. Per questo motivo, utilizza il `pointerIndex` per ottenere il puntatore `id` valore il `GetPointerId` metodo. Questo ID *è* coerente tra più eventi:

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

Si noti che utilizza la sostituzione di `ActionMasked` proprietà nel `switch` istruzione anziché `Action` proprietà. La ragione di questo comportamento è la seguente:

Quando si gestiscono Multi-touch, il `Action` proprietà ha un valore `MotionEventsAction.Down` per il primo dito tocco sullo schermo e i valori di `Pointer2Down` e `Pointer3Down` come il seconda e terza dita anche toccano lo schermo. Come verificare il quarta e quinta dita contatto, il `Action` proprietà presenta valori numerici che non corrispondono anche a membri del `MotionEventsAction` enumerazione! È necessario esaminare i valori di flag di bit di valori per interpretare il significato.

Analogamente, le dita lasciato contatto con la schermata, il `Action` ha i valori della proprietà `Pointer2Up` e `Pointer3Up` per il seconda e terza dita, e `Up` per il primo dito.

Il `ActionMasked` proprietà assume un minore numero di valori, perché è progettato per essere utilizzato in combinazione con la `ActionIndex` proprietà distinguere tra più dita. Quando il dito tocca lo schermo, la proprietà può essere solo uguale `MotionEventActions.Down` per il primo dito e `PointerDown` per dita successive. Come le dita esca dalla schermata, `ActionMasked` ha valori di `Pointer1Up` per le successive DITA e `Up` per il primo dito.

Quando si utilizza `ActionMasked`, `ActionIndex` distingue tra le dita successivi per il tocco e lasciare la schermata, ma è in genere non è necessario utilizzare tale valore, ad eccezione come argomento agli altri metodi nel `MotionEvent` oggetto. Per Multi-touch, uno dei più importanti di questi metodi è `GetPointerId` chiamato nel codice precedente. Che restituisce un valore che è possibile utilizzare per una chiave del dizionario per associare eventi specifici dita.

Il `OnTouchEvent` esegue l'override nel [Pittura con le dita](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programmare i processi di `MotionEventActions.Down` e `PointerDown` gli eventi in modo identico creando un nuovo `FingerPaintPolyline` oggetto e aggiungerla al dizionario:

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

Si noti che il `pointerIndex` viene inoltre utilizzato per ottenere la posizione del dito all'interno della visualizzazione. Tutte le informazioni di contatto sono associate il `pointerIndex` valore. Il `id` identifica dita tra più messaggi, in modo che utilizzato per creare la voce di dizionario.

Analogamente, il `OnTouchEvent` override anche gli handle il `MotionEventActions.Up` e `Pointer1Up` in modo identico trasferendo la polilinea completata per il `completedPolylines` raccolta in modo che possono essere disegnate durante il `OnDraw` eseguire l'override. Nel codice viene rimossa anche la `id` voce dal dizionario:

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

Ora per la parte più impegnativa.

Tra la freccia giù e gli eventi in genere sono presenti molti `MotionEventActions.Move` eventi. Questi sono inclusi in una singola chiamata a `OnTouchEvent`, e devono essere gestiti in modo diverso dal `Down` e `Up` eventi. Il `pointerIndex` ottenuto in precedenza dal valore di `ActionIndex` proprietà deve essere ignorata. È invece il metodo deve ottenere più `pointerIndex` valori Loop compreso tra 0 e il `PointerCount` proprietà, quindi ottenere un `id` per ognuno di questi `pointerIndex` valori:

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

Questo tipo di elaborazione consente di [Pittura con le dita](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programma per tenere traccia delle dita singoli e tracciare i risultati sullo schermo:

[![Schermata di esempio dall'esempio Pittura con le dita](touch-tracking-images/image01.png)](touch-tracking-images/image01.png)

Abbiamo visto come è possibile tenere traccia delle singole dita sullo schermo e distinguere tra di essi.


## <a name="related-links"></a>Collegamenti correlati

- [Guida di iOS equivalente Xamarin](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Pittura con le dita (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
