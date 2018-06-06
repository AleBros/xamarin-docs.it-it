---
title: macOS API per gli sviluppatori Xamarin.Mac
description: Questo documento viene descritto come leggere i selettori Objective-C e come individuare i metodi c# corrispondenti in un'app Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/02/2017
ms.openlocfilehash: cceaa2f6e89b712be5929f7e978663d8c47f18c5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791550"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>macOS API per gli sviluppatori Xamarin.Mac

## <a name="overview"></a>Panoramica

Per la maggior parte del tempo allo sviluppo con Xamarin.Mac, può pensare, lettura e scrittura in c# senza preoccuparsi con le API sottostanti Objective-C. Tuttavia, talvolta si necessario leggere la documentazione dell'API di Apple, convertire una risposta dall'Overflow dello Stack a una soluzione per il problema oppure confrontare a un campione esistente.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>Durante la lettura è pericolosa sufficiente Objective-C.

In alcuni casi sarà necessaria per leggere una definizione di Objective-C o metodo chiamare e tradurre che il metodo c# equivalente. Si esamina una definizione di funzione Objective-C e suddividere le parti. Questo metodo (un *selettore* in Objective-C) possono trovarsi in `NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

La dichiarazione può essere letti da sinistra a destra:

- Il `-` prefisso e, pertanto un metodo di istanza (non statici). + indica un metodo di classe (statico)
- `(BOOL)` è il tipo restituito (bool in c#)
- `canDragRowsWithIndexes` è la prima parte del nome.
- `(NSIndexSet *)rowIndexes` è il primo param e con esso è di tipo. Il primo parametro è nel formato: `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` è il secondo parametro e il relativo tipo. Ogni parametro dopo il primo è il formato: `selectorPart:(Type) pararmName`
- Il nome completo del selettore messaggio è: `canDragRowsWithIndexes:atPoint:`. Si noti il `:` alla fine, è importante.
- L'associazione Xamarin.Mac c# effettivo è: `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Questa chiamata selettore può essere letti allo stesso modo:

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- L'istanza `v` si sono verificati relativi `canDragRowsWithIndexes:atPoint` selettore chiamato con due parametri, `set` e `point`, passato.
- In c#, la chiamata del metodo simile al seguente: `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>Trovare il membro c# per un selettore specificato

Ora che è stato individuato il selettore di Objective-C, che è necessario richiamare, il passaggio successivo consiste nell'associare che per il membro equivalente di c#. Esistono quattro possibili approcci è possibile provare (continuare con la `NSTableView CanDragRows` esempio):

1. Utilizzare l'elenco di completamento automatico per individuare rapidamente un elemento con lo stesso nome. Poiché si sa è un'istanza di `NSTableView` è possibile digitare:

    - `NSTableView x;`
    - `x.` [ctrl + spazio se non viene visualizzato l'elenco).
    - `CanDrag` [Immettere]
    - Il pulsante destro del metodo, Vai a dichiarazione per aprire il Browser di Assembly in cui è possibile confrontare il `Export` attributo per il selettore in questione

2. Ricerca l'associazione dell'intera classe. Poiché si sa è un'istanza di `NSTableView` è possibile digitare:

    - `NSTableView x;`
    - Fare doppio clic su `NSTableView`, Vai a dichiarazione al Browser di Assembly
    - Ricerca per il selettore in questione

3. È possibile utilizzare il [documentazione online di API Xamarin.Mac](https://developer.xamarin.com/api/root/monomac-lib/) .

4. Miguel fornisce una visualizzazione "Rosetta pietra" APIs Xamarin.Mac [qui](http://tirania.org/tmp/rosetta.html) che è possibile cercare tramite un'API specificata. Se l'API non è AppKit o macOS specifico, potrebbe essere presente.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
