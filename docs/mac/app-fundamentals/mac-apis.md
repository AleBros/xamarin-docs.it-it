---
title: API macOS per sviluppatori Novell. Mac
description: Questo documento descrive come leggere i selettori Objective-C e come trovare i metodi C# corrispondenti in un'app Novell. Mac.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/02/2017
ms.openlocfilehash: c7dfa87d2fa4e3e5b917029451a081640a552cce
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280996"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>API macOS per sviluppatori Novell. Mac

## <a name="overview"></a>Panoramica

Per la maggior parte del tempo di sviluppo con Novell. Mac, è possibile pensare, leggere e scrivere C# senza preoccuparsi delle API Objective-C sottostanti. Tuttavia, a volte è necessario leggere la documentazione dell'API da Apple, tradurre una risposta da Stack Overflow a una soluzione per il problema oppure confrontarla con un esempio esistente.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>Lettura di un numero sufficiente di Objective-C come pericoloso

In alcuni casi sarà necessario leggere una definizione Objective-C o una chiamata al metodo e tradurla nel metodo C# equivalente. Diamo un'occhiata a una definizione di funzione Objective-C e suddivido le parti. Questo metodo (un *selettore* in Objective-C) si trova `NSTableView`in:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

La dichiarazione può essere letta da sinistra a destra:

- Il `-` prefisso indica che si tratta di un metodo di istanza (non statico). + indica che si tratta di un metodo di classe (statico)
- `(BOOL)`tipo restituito (bool in C#)
- `canDragRowsWithIndexes`è la prima parte del nome.
- `(NSIndexSet *)rowIndexes`è il primo parametro e con il tipo. Il primo parametro è nel formato:`(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint`è il secondo parametro e il relativo tipo. Ogni parametro dopo il primo è il formato:`selectorPart:(Type) pararmName`
- Il nome completo di questo selettore di `canDragRowsWithIndexes:atPoint:`messaggi è:. `:` Si noti che alla fine è importante.
- Il binding Novell. Mac C# effettivo è:`bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Questa chiamata al selettore può essere letta nello stesso modo:

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- Il `v` `canDragRowsWithIndexes:atPoint` selettore dell'istanza viene chiamato con due parametri `set` , `point`e, passati.
- In C#la chiamata al metodo ha un aspetto simile al seguente:`x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>Ricerca del C# membro per un selettore specificato

Ora che è stato trovato il selettore Objective-C che è necessario richiamare, il passaggio successivo consiste nel mapping al C# membro equivalente. Sono disponibili quattro approcci che è possibile provare (continuando `NSTableView CanDragRows` con l'esempio):

1. Usare l'elenco di completamento automatico per analizzare rapidamente un elemento con lo stesso nome. Poiché è noto che si tratta di un' `NSTableView` istanza di, è possibile digitare:

    - `NSTableView x;`
    - `x.`[CTRL + barra spaziatrice se l'elenco non viene visualizzato).
    - `CanDrag`entrare
    - Fare clic con il pulsante destro del mouse sul metodo, scegliere Dichiarazione per aprire il browser assembly in `Export` cui è possibile confrontare l'attributo con il selettore in questione.

2. Eseguire una ricerca nell'intera associazione di classe. Poiché è noto che si tratta di un' `NSTableView` istanza di, è possibile digitare:

    - `NSTableView x;`
    - Fare clic con `NSTableView`il pulsante destro del mouse, scegliere dichiarazione in assembly browser
    - Cerca il selettore in questione

3. È possibile usare la [documentazione online dell'API Novell. Mac](https://docs.microsoft.com/dotnet/api/?view=xamarinmac-3.0) .

4. Miguel fornisce una visualizzazione "Rosetta Stone" delle API di Novell. Mac [in cui è](https://tirania.org/tmp/rosetta.html) possibile eseguire ricerche per un'API specifica. Se l'API non è specifica di AppKit o macOS, potrebbe trovarlo qui.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
