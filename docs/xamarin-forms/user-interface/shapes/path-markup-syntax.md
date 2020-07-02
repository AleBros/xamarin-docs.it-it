---
title: 'Xamarin.FormsForme: sintassi di markup del percorso'
description: Xamarin.FormsLa sintassi di markup del percorso consente di specificare in modo compatto le geometrie del percorso in XAML.
ms.prod: xamarin
ms.assetid: A2C1BD59-1A16-4E26-A825-0338E2AF9E65
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 68b7f4a245a60df1723f5a6442f30dc2b1a15932
ms.sourcegitcommit: 91b4d2f93687fadec5c3f80aadc8f7298d911624
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85794980"
---
# <a name="xamarinforms-shapes-path-markup-syntax"></a>Xamarin.FormsForme: sintassi di markup del percorso

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scarica esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Xamarin.Formsla sintassi di markup del percorso consente di specificare in modo compatto le geometrie del percorso in XAML. La sintassi viene specificata come valore stringa per la `Path.Data` proprietà:

```xaml
<Path Stroke="Black"
      Data="M13.908992,16.207977 L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983Z" />
```

La sintassi di markup del percorso è costituita da un `FillRule` valore facoltativo e da una o più descrizioni di figure. Questa sintassi può essere espressa come: `<Path Data="` *[FillRule]* *figureDescription* *[figureDescription]* * `" ... />`

In questa sintassi:

- *FillRule* è un valore facoltativo `Xamarin.Forms.Shapes.FillRule` che specifica se la geometria deve usare `EvenOdd` o `Nonzero` `FillRule` . `F0`viene usato per specificare la `EvenOdd` regola di riempimento, mentre `F1` viene usato per specificare la `Nonzero` regola di riempimento. Per ulteriori informazioni sulle regole di riempimento, vedere [ Xamarin.Forms forme: regole di riempimento](fillrules.md).
- *figureDescription* rappresenta una figura composta da un comando Move, da un comando di richiamo e da un comando di chiusura facoltativo. Un comando Move specifica il punto iniziale della figura. I comandi di disegni descrivono il contenuto della figura e il comando di chiusura facoltativo chiude la figura.

Nell'esempio precedente, la sintassi di markup del percorso specifica un punto iniziale usando il comando Move ( `M` ), una serie di linee rette usando il comando line ( `L` ) e chiude il percorso con il comando Close ( `Z` ).

Nella sintassi di markup del percorso gli spazi non sono necessari prima o dopo i comandi. Inoltre, due numeri non devono essere separati da una virgola o da uno spazio vuoto, ma questa operazione può essere eseguita solo quando la stringa non è ambigua.

> [!TIP]
> Path Markup Language usa una sintassi compatibile con le definizioni del percorso dell'immagine Scalable Vector Graphics (SVG) e pertanto può essere utile per il porting di elementi grafici dal formato SVG.

## <a name="move-command"></a>Comando di spostamento

Il comando Move specifica il punto iniziale di una nuova figura. La sintassi per questo comando è: `M` *StartPoint* o `m` *StartPoint*.

In questa sintassi, *StartPoint* è una [`Point`](xref:Xamarin.Forms.Point) struttura che specifica il punto iniziale di una nuova figura. Se si elencano più punti dopo il comando Move, viene disegnata una linea a questi punti.

`M 10,10`è un esempio di comando Move valido.

## <a name="draw-commands"></a>Comandi di disegno

Un comando di disegno può essere costituito da diversi comandi di forma. Sono disponibili i comandi di estrazione seguenti:

- Riga ( `L` o `l` ).
- Linea orizzontale ( `H` o `h` ).
- Linea verticale ( `V` o `v` ).
- Curva di Bezier cubica ( `C` o `c` ).
- Curva di Bézier quadratica ( `Q` o `q` ).
- Curva di Bezier cubica uniforme ( `S` o `s` ).
- Curva di Bezier quadratica smussata ( `T` o `t` ).
- Arco ellittico ( `A` o `a` ).

Ogni comando di estrazione viene specificato con una lettera senza distinzione tra maiuscole e minuscole. Quando immetti in sequenza più di un comando dello stesso tipo, puoi omettere la voce di comando duplicata. Ad esempio `L 100,200 300,400` , equivale a `L 100,200 L 300,400` .

### <a name="line-command"></a>Comando di linea

Il comando di riga crea una linea retta tra il punto corrente e il punto finale specificato. La sintassi per questo comando è: `L` *endpoint* o `l` *endpoint*.

In questa sintassi, *endpoint* è un oggetto [`Point`](xref:Xamarin.Forms.Point) che rappresenta il punto finale della linea.

`L 20,30`e `L 20 30` sono esempi di comandi di riga validi.

### <a name="horizontal-line-command"></a>Comando di linea orizzontale

Il comando linea orizzontale crea una linea orizzontale tra il punto corrente e la coordinata x specificata. La sintassi per questo comando è: `H` *x* o `h` *x*.

In questa sintassi, *x* è un oggetto `double` che rappresenta la coordinata x del punto finale della linea.

`H 90` è un esempio di comando di linea orizzontale valido.

### <a name="vertical-line-command"></a>Comando di linea verticale

Il comando linea verticale crea una linea verticale tra il punto corrente e la coordinata y specificata. La sintassi per questo comando è: `V` *y* o `v` *y*.

In questa sintassi, *y* è un oggetto `double` che rappresenta la coordinata y del punto finale della linea.

`V 90` è un esempio di comando di linea verticale valido.

### <a name="cubic-bezier-curve-command"></a>Comando curva di Bezier cubica

Il comando della curva di Bezier cubica crea una curva di Bezier cubica tra il punto corrente e il punto finale specificato usando i due punti di controllo specificati. La sintassi per questo comando è: `C` *controlPoint1* *controlPoint2* *endpoint* o `c` *controlPoint1* *controlPoint2* *endpoint*.

In questa sintassi:

- *controlPoint1* è un oggetto [`Point`](xref:Xamarin.Forms.Point) che rappresenta il primo punto di controllo della curva, che determina la tangente iniziale della curva.
- *controlPoint2* è un oggetto [`Point`](xref:Xamarin.Forms.Point) che rappresenta il secondo punto di controllo della curva, che determina la tangente finale della curva.
- l' *endpoint* è un oggetto [`Point`](xref:Xamarin.Forms.Point) che rappresenta il punto in cui viene disegnata la curva.

`C 100,200 200,400 300,200`è un esempio di comando di curva di Bezier cubica valido.

### <a name="quadratic-bezier-curve-command"></a>Comando curva di Bezier quadratica

Il comando di curva di Bézier quadratica crea una curva di Bézier quadratica tra il punto corrente e il punto finale specificato usando il punto di controllo specificato. La sintassi per questo comando è: `Q` *controlPoint* *endpoint* ControlPoint o `q` *controlPoint* *endpoint*ControlPoint.

In questa sintassi:

- *ControlPoint* è un oggetto [`Point`](xref:Xamarin.Forms.Point) che rappresenta il punto di controllo della curva, che determina la tangente iniziale e finale della curva.
- l' *endpoint* è un oggetto [`Point`](xref:Xamarin.Forms.Point) che rappresenta il punto in cui viene disegnata la curva.

`Q 100,200 300,200` è un esempio di comando di curva di Bézier quadratica valido.

### <a name="smooth-cubic-bezier-curve-command"></a>Comando Smooth cubic Bezier curve

Il comando Smooth cubic Bezier curve crea una curva di Bezier cubica tra il punto corrente e il punto finale specificato usando il punto di controllo specificato. La sintassi per questo comando è: `S` *controlPoint2* *endpoint* ControlPoint2 o `s` *controlPoint2* *endpoint*ControlPoint2.  

In questa sintassi:

- *controlPoint2* è un oggetto [`Point`](xref:Xamarin.Forms.Point) che rappresenta il secondo punto di controllo della curva, che determina la tangente finale della curva.
- l' *endpoint* è un oggetto [`Point`](xref:Xamarin.Forms.Point) che rappresenta il punto in cui viene disegnata la curva.

Si presuppone che il primo punto di controllo sia la reflection del secondo punto di controllo del comando precedente, relativo al punto corrente. Se non è presente alcun comando precedente oppure il comando precedente non era un comando di curva di Bezier cubica o un comando di curva di Bezier cubica uniforme, il primo punto di controllo si presuppone coincidere con il punto corrente.

`S 100,200 200,300`è un esempio di comando di curva di Bezier cubica valido.

### <a name="smooth-quadratic-bezier-curve-command"></a>Comando curva di Bezier quadratica uniforme

Il comando di curva di Bézier quadratica uniforme crea una curva di Bézier quadratica tra il punto corrente e il punto finale specificato usando un punto di controllo. La sintassi per questo comando è: `T` *endpoint* o `t` *endpoint*.

In questa sintassi, *endpoint* è un oggetto [`Point`](xref:Xamarin.Forms.Point) che rappresenta il punto in cui viene disegnata la curva.

Il punto di controllo viene considerato come reflection del punto di controllo del comando precedente relativo al punto corrente. Se non è presente alcun comando precedente o se il comando precedente non era una curva di Bézier quadratica o un comando curva di Bezier quadratica uniforme, il punto di controllo si presuppone coincidere con il punto corrente.

`T 100,30`è un esempio di un comando Smooth quadratico di Bézier cubicamente valido.

### <a name="elliptical-arc-command"></a>Comando di arco ellittico

Il comando di arco ellittico crea un arco ellittico tra il punto corrente e il punto finale specificato. La sintassi per questo comando è: `A` *size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *endpoint* sweepDirectionFlag o `a` *size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *endpoint*.

In questa sintassi:

- `size`è un oggetto [`Size`](xref:Xamarin.Forms.Size) che rappresenta il raggio x e y dell'arco.
- `rotationAngle`è un oggetto `double` che rappresenta la rotazione dell'ellisse, in gradi.
- `isLargeArcFlag`deve essere impostato su 1 se l'angolo dell'arco deve essere maggiore o uguale a 180. in caso contrario, impostarlo su 0.
- `sweepDirectionFlag`deve essere impostato su 1 se l'arco viene disegnato in una direzione di angolo positivo. in caso contrario, impostarlo su 0.
- `endPoint`è un oggetto [`Point`](xref:Xamarin.Forms.Point) a cui viene disegnato l'arco.

`A 150,150 0 1,0 150,-150`è un esempio di un comando di arco ellittico valido.

## <a name="close-command"></a>Comando di chiusura

Il comando Close termina la figura corrente e crea una riga che connette il punto corrente al punto iniziale della figura. Questo comando crea pertanto un join di riga tra l'ultimo segmento e il primo segmento della figura.

La sintassi per il comando Close è: `Z` o `z` .

## <a name="additional-values"></a>Valori aggiuntivi

Invece di un valore numerico standard, è anche possibile usare i seguenti valori speciali con distinzione tra maiuscole e minuscole:

- `Infinity`rappresenta `double.PositiveInfinity` .
- `-Infinity`rappresenta `double.NegativeInfinity` .
- `NaN`rappresenta `double.NaN` .

Inoltre, è anche possibile usare la notazione scientifica senza distinzione tra maiuscole e minuscole. Pertanto, `+1.e17` è un valore valido.

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsForme: geometrie](geometries.md)
- [Xamarin.FormsForme: regole di riempimento](fillrules.md)
