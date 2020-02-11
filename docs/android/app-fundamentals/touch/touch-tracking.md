---
title: Rilevamento del dito multitocco in Xamarin.Android
description: Questo argomento illustra come tenere traccia degli eventi di tocco da più dita
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: f960c3cec90bd331f5a1433a869c7720b40c9680
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024273"
---
# <a name="multi-touch-finger-tracking"></a>Rilevamento del dito multitocco

_Questo argomento illustra come tenere traccia degli eventi di tocco da più dita_

In alcuni casi un'applicazione multitocco deve tenere traccia delle singole dita mentre si spostano simultaneamente sullo schermo. Una tipica applicazione è un programma di disegno con dito. Si vuole che l'utente sia in grado di creare con un solo dito, ma anche di creare con più dita contemporaneamente. Man mano che il programma elabora più eventi Touch, deve distinguere gli eventi corrispondenti a ogni dito. Android fornisce un codice ID per questo scopo, ma il recupero e la gestione di tale codice può essere un po' difficile.

Per tutti gli eventi associati a un particolare dito, il codice ID rimane lo stesso. Il codice ID viene assegnato quando un dito tocca lo schermo per la prima volta e non è più valido dopo l'accuratezza del dito dallo schermo.
Questi codici ID sono in genere numeri interi molto piccoli e Android li riutilizza per gli eventi di tocco successivi.

Quasi sempre, un programma che tiene traccia delle singole dita mantiene un dizionario per il rilevamento del tocco. La chiave del dizionario è il codice ID che identifica un dito particolare. Il valore del dizionario dipende dall'applicazione. Nel programma [FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) , ogni tratto del dito (dal tocco alla versione) viene associato a un oggetto che contiene tutte le informazioni necessarie per eseguire il rendering della linea disegnata con tale dito. Il programma definisce una classe di `FingerPaintPolyline` ridotta a questo scopo:

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

Ogni polilinea ha un colore, una larghezza del tratto e un oggetto [`Path`](xref:Android.Graphics.Path) grafica Android per accumulare ed eseguire il rendering di più punti della linea mentre viene disegnata.

Il resto del codice riportato di seguito è contenuto in un `View` derivato denominato `FingerPaintCanvasView`. Tale classe mantiene un dizionario di oggetti di tipo `FingerPaintPolyline` nel momento in cui vengono attivamente disegnati da una o più dita:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Questo dizionario consente alla visualizzazione di ottenere rapidamente le informazioni `FingerPaintPolyline` associate a un dito particolare.

La classe `FingerPaintCanvasView` gestisce anche un oggetto `List` per le polilinee completate:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Gli oggetti in questo `List` sono nello stesso ordine in cui sono stati disegnati.

`FingerPaintCanvasView` esegue l'override di due metodi definiti da `View`: [`OnDraw`](xref:Android.Views.View.OnDraw*)
e [`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*).
Nel `OnDraw` override, la vista disegna le polilinee completate e quindi disegna le polilinee in corso.

L'override del metodo `OnTouchEvent` inizia ottenendo un valore `pointerIndex` dalla proprietà `ActionIndex`. Questo `ActionIndex` valore distingue tra più dita, ma non è coerente tra più eventi. Per questo motivo, utilizzare il `pointerIndex` per ottenere il puntatore `id` valore dal metodo `GetPointerId`. Questo ID *è* coerente tra più eventi:

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

Si noti che la sostituzione usa la proprietà `ActionMasked` nell'istruzione `switch` invece che nella proprietà `Action`. La ragione di questo comportamento è la seguente:

Quando si lavora con il multitocco, il `Action` proprietà ha il valore `MotionEventsAction.Down` per il primo dito per toccare lo schermo, quindi i valori di `Pointer2Down` e `Pointer3Down` come il secondo e il terzo dito toccano anche lo schermo. Poiché il quarto e il quinto dito fanno contatto, la proprietà `Action` dispone di valori numerici che non corrispondono neanche ai membri dell'enumerazione `MotionEventsAction`. È necessario esaminare i valori dei flag di bit nei valori per interpretare il significato.

Analogamente, quando le dita lasciano il contatto con lo schermo, la proprietà `Action` dispone di valori `Pointer2Up` e `Pointer3Up` per la seconda e la terza barretta e `Up` per il primo dito.

Il `ActionMasked` proprietà accetta un numero minore di valori perché è destinato a essere usato insieme alla proprietà `ActionIndex` per distinguere tra più dita. Quando le dita toccano lo schermo, la proprietà può essere uguale `MotionEventActions.Down` solo per il primo dito e `PointerDown` per le dita successive. Quando le dita lasciano lo schermo, `ActionMasked` dispone di valori `Pointer1Up` per le dita successive e `Up` per il primo dito.

Quando si usa `ActionMasked`, il `ActionIndex` distingue tra le dita successive per toccare e lasciare lo schermo, ma in genere non è necessario usare tale valore tranne come argomento per altri metodi nell'oggetto `MotionEvent`. Per la funzionalità multitocco, uno dei più importanti di questi metodi è `GetPointerId` viene chiamato nel codice riportato sopra. Tale metodo restituisce un valore che è possibile usare per una chiave del dizionario per associare eventi specifici alle dita.

Il `OnTouchEvent` override nel programma [FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) elabora gli eventi `MotionEventActions.Down` e `PointerDown` in modo identico creando un nuovo oggetto `FingerPaintPolyline` e aggiungendolo al dizionario:

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

Si noti che il `pointerIndex` viene usato anche per ottenere la posizione del dito nella visualizzazione. Tutte le informazioni sul tocco sono associate al valore `pointerIndex`. Il `id` identifica in modo univoco le dita tra più messaggi, quindi viene usato per creare la voce del dizionario.

Analogamente, l'override del `OnTouchEvent` gestisce anche i `MotionEventActions.Up` e `Pointer1Up` in modo identico trasferendo la polilinea completata alla raccolta `completedPolylines` in modo che possano essere disegnati durante l'override del `OnDraw`. Il codice rimuove anche la voce `id` dal dizionario:

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

A questo punto, per la parte complicata.

Tra gli eventi down e up, in genere si verificano molti eventi `MotionEventActions.Move`. Sono raggruppati in una singola chiamata a `OnTouchEvent`e devono essere gestiti in modo diverso rispetto agli eventi `Down` e `Up`. Il valore `pointerIndex` ottenuto in precedenza dalla proprietà `ActionIndex` deve essere ignorato. Al contrario, il metodo deve ottenere più valori `pointerIndex` eseguendo un ciclo tra 0 e la proprietà `PointerCount` e quindi ottenere un `id` per ognuno dei valori `pointerIndex`:

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

Questo tipo di elaborazione consente al programma [FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) di tenere traccia delle singole dita e di tracciare i risultati sullo schermo:

[schermata di esempio![dell'esempio FingerPaint](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

A questo punto si è appreso come è possibile tenere traccia delle singole dita sullo schermo e distinguerle tra loro.

## <a name="related-links"></a>Collegamenti correlati

- [Guida a Xamarin iOS equivalente](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
