---
title: macOS, le API per gli sviluppatori di xamarin. Mac
description: Questo documento descrive come leggere i selettori Objective-C e come trovare i relativi metodi c# in un'app xamarin. Mac.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/02/2017
ms.openlocfilehash: c387bbead1ac56d7f4c4c05a79c430302e50aec1
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668283"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>macOS, le API per gli sviluppatori di xamarin. Mac

## <a name="overview"></a>Panoramica

Per la maggior parte del tempo allo sviluppo con xamarin. Mac, è possibile pensare, leggere e scrivere in c# senza preoccuparsi con le API Objective-C sottostante. Tuttavia, in alcuni casi sarà necessario leggere la documentazione dell'API di Apple, tradurre una risposta di Stack Overflow per una soluzione al problema o confrontare con un esempio esistente.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>La lettura sufficiente Objective-C per rappresentare un pericolo

In alcuni casi sarà necessario leggere una definizione di Objective-C o metodo chiamare e traduce al metodo c# equivalente. È possibile osservare una definizione di funzione Objective-C e suddividere le parti. Questo metodo (un *selettore* in Objective-C) è reperibile nella `NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

La dichiarazione può essere letto da sinistra a destra:

- Il `-` prefisso significa che è un metodo di istanza (non statico). + indica un metodo di classe (statico)
- `(BOOL)` è il tipo restituito (bool in c#)
- `canDragRowsWithIndexes` è la prima parte del nome.
- `(NSIndexSet *)rowIndexes` è il primo parametro e con essa è di tipo. Il primo parametro è nel formato: `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` è il secondo parametro e il relativo tipo. Ogni parametro dopo il primo è il formato: `selectorPart:(Type) pararmName`
- Il nome completo del selettore messaggio è: `canDragRowsWithIndexes:atPoint:`. Si noti il `:` alla fine, è importante.
- L'effettiva associazione di xamarin. Mac in c# è: `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Questa chiamata selettore può essere letti allo stesso modo:

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- L'istanza `v` ha riscontrato relativi `canDragRowsWithIndexes:atPoint` selettore chiamato con due parametri, `set` e `point`, in passato.
- In c#, la chiamata al metodo è simile al seguente: `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>Trovare il membro c# per un selettore specificato

Ora che si trovano il selettore di Objective-C che è necessario richiamare, il passaggio successivo è associare che al membro c# equivalente. Esistono quattro possibili approcci è possibile provare a (continuare con la `NSTableView CanDragRows` riportato):

1. Usare l'elenco di completamento automatico per individuare rapidamente un elemento con lo stesso nome. Poiché si sa è un'istanza di `NSTableView` è possibile digitare:

    - `NSTableView x;`
    - `x.` [ctrl + barra spaziatrice se non viene visualizzato l'elenco).
    - `CanDrag` [enter]
    - Il pulsante destro del metodo, Vai a dichiarazione per aprire il Browser di Assembly in cui è possibile confrontare il `Export` attributo per il selettore in questione

2. Ricerca l'associazione dell'intera classe. Poiché si sa è un'istanza di `NSTableView` è possibile digitare:

    - `NSTableView x;`
    - Fare doppio clic su `NSTableView`, Vai a dichiarazione al Browser di Assembly
    - Ricerca per il selettore in questione

3. È possibile usare la [documentazione online di API xamarin. Mac](https://docs.microsoft.com/dotnet/api/?view=xamarinmac-3.0) .

4. Miguel fornisce una visualizzazione "Rosetta Stone" APIs Xamarin.Mac [qui](https://tirania.org/tmp/rosetta.html) che è possibile cercare tramite un'API specifica. Se l'API non AppKit o macOS specifico, si può cercarla.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
