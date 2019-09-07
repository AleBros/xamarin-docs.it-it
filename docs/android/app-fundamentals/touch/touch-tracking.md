---
title: Rilevamento del dito multitocco in Novell. Android
description: Questo argomento illustra come tenere traccia degli eventi di tocco da più dita
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 6dd3bf848d38f0211dcda100994f6f7ec8831fce
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754684"
---
# <a name="multi-touch-finger-tracking"></a>Rilevamento del dito multitocco

_Questo argomento illustra come tenere traccia degli eventi di tocco da più dita_

In alcuni casi un'applicazione multitocco deve tenere traccia delle singole dita mentre si spostano simultaneamente sullo schermo. Una tipica applicazione è un programma di disegno con dito. Si vuole che l'utente sia in grado di creare con un solo dito, ma anche di creare con più dita contemporaneamente. Man mano che il programma elabora più eventi Touch, deve distinguere gli eventi corrispondenti a ogni dito. Android fornisce un codice ID per questo scopo, ma il recupero e la gestione di tale codice può essere un po' difficile.

Per tutti gli eventi associati a un particolare dito, il codice ID rimane lo stesso. Il codice ID viene assegnato quando un dito tocca lo schermo per la prima volta e non è più valido dopo l'accuratezza del dito dallo schermo.
Questi codici ID sono in genere numeri interi molto piccoli e Android li riutilizza per gli eventi di tocco successivi.

Quasi sempre, un programma che tiene traccia delle singole dita mantiene un dizionario per il rilevamento del tocco. La chiave del dizionario è il codice ID che identifica un dito particolare. Il valore del dizionario dipende dall'applicazione. Nel programma [FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) , ogni tratto del dito (dal tocco alla versione) viene associato a un oggetto che contiene tutte le informazioni necessarie per eseguire il rendering della linea disegnata con tale dito. Il programma definisce una classe `FingerPaintPolyline` di piccole dimensioni a questo scopo:

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

Ogni polilinea ha un colore, una larghezza del tratto e un oggetto [`Path`](xref:Android.Graphics.Path) grafico Android per accumulare ed eseguire il rendering di più punti della linea mentre viene disegnata.

Il resto del codice riportato di seguito è contenuto in una `View` derivata denominata `FingerPaintCanvasView`. Tale classe mantiene un dizionario di oggetti di tipo `FingerPaintPolyline` durante il tempo in cui vengono tracciati attivamente da una o più dita:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Questo dizionario consente alla visualizzazione di ottenere rapidamente le `FingerPaintPolyline` informazioni associate a un dito particolare.

La `FingerPaintCanvasView` classe gestisce anche un `List` oggetto per le polilinee che sono state completate:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Gli oggetti in questo `List` oggetto sono nello stesso ordine in cui sono stati disegnati.

`FingerPaintCanvasView`esegue l'override di due `View`metodi definiti da:[`OnDraw`](xref:Android.Views.View.OnDraw*)
e [`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*).
`OnDraw` Nell'override, la vista disegna le polilinee completate e quindi disegna le polilinee in corso.

L'override del `OnTouchEvent` metodo inizia ottenendo un `pointerIndex` valore dalla `ActionIndex` proprietà. Questo `ActionIndex` valore distingue tra più dita, ma non è coerente tra più eventi. Per questo motivo, si usa `pointerIndex` per ottenere il valore del puntatore `id` dal `GetPointerId` metodo. Questo ID *è* coerente tra più eventi:

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

Si noti che la sostituzione USA `ActionMasked` la proprietà `switch` nell'istruzione anziché la `Action` proprietà. La ragione di questo comportamento è la seguente:

Quando si usa la funzionalità `Action` multitocco, la proprietà ha un `MotionEventsAction.Down` valore per il primo dito per toccare lo schermo, quindi i valori di `Pointer2Down` e `Pointer3Down` come la seconda e la terza barretta toccano anche lo schermo. Poiché il quarto e il quinto dito fanno contatto, `Action` la proprietà dispone di valori numerici che non corrispondono neanche ai `MotionEventsAction` membri dell'enumerazione. È necessario esaminare i valori dei flag di bit nei valori per interpretare il significato.

Analogamente, quando le dita lasciano il contatto con lo schermo `Action` , la proprietà ha `Pointer2Up` i `Pointer3Up` valori e per la seconda e la terza `Up` barretta e per il primo dito.

La `ActionMasked` proprietà accetta un numero minore di valori perché deve essere usato insieme `ActionIndex` alla proprietà per distinguere tra più dita. Quando le dita toccano lo schermo, la proprietà può `MotionEventActions.Down` essere uguale solo per il `PointerDown` primo dito e per le dita successive. Quando le dita lasciano lo schermo, `ActionMasked` presenta i valori `Pointer1Up` di per le dita successive `Up` e per il primo dito.

Quando si `ActionMasked`USA, `ActionIndex` l' `MotionEvent` oggetto distingue tra le dita successive per toccare e lasciare lo schermo, ma in genere non è necessario usare tale valore tranne che come argomento per altri metodi dell'oggetto. Per la funzionalità multitocco, uno dei più importanti di questi metodi viene `GetPointerId` chiamato nel codice riportato sopra. Tale metodo restituisce un valore che è possibile usare per una chiave del dizionario per associare eventi specifici alle dita.

L' `OnTouchEvent` override nel programma [FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) elabora gli `MotionEventActions.Down` eventi e `PointerDown` in modo identico creando un nuovo `FingerPaintPolyline` oggetto e aggiungendolo al dizionario:

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

`pointerIndex` Si noti che viene usato anche per ottenere la posizione del dito nella visualizzazione. Tutte le informazioni sul tocco sono associate `pointerIndex` al valore. Identifica in modo univoco le dita tra più messaggi, quindi viene usato per creare la voce del dizionario. `id`

Analogamente, `OnTouchEvent` l'override `MotionEventActions.Up` gestisce anche e `Pointer1Up` in modo identico trasferendo la polilinea completata alla `completedPolylines` raccolta in modo che possa `OnDraw` essere disegnata durante l'override. Il codice rimuove anche la `id` voce dal dizionario:

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

Tra gli eventi down e up, in genere si verificano molti `MotionEventActions.Move` eventi. Sono raggruppati in una singola chiamata a `OnTouchEvent`e devono essere gestiti in modo diverso `Down` dagli eventi e `Up` . Il `pointerIndex` valore ottenuto `ActionIndex` in precedenza dalla proprietà deve essere ignorato. Al contrario, il metodo deve ottenere `pointerIndex` più valori eseguendo `pointerIndex` un `id` ciclo compreso tra 0 `PointerCount` e la proprietà, quindi ottenere per ognuno dei valori seguenti:

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

[![Schermata di esempio dell'esempio FingerPaint](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

A questo punto si è appreso come è possibile tenere traccia delle singole dita sullo schermo e distinguerle tra loro.

## <a name="related-links"></a>Collegamenti correlati

- [Guida a Novell iOS equivalente](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
