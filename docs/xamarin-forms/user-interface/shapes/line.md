---
title: 'Xamarin.FormsForme: linea'
description: La Xamarin.Forms classe line può essere utilizzata per creare linee.
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f97e6930f543f47383e2b8da09b36573bd3192d0
ms.sourcegitcommit: ef3d4a70e70927c4f231b763842c5355f1571d15
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243750"
---
# <a name="xamarinforms-shapes-line"></a>Xamarin.FormsForme: linea

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Line` classe deriva dalla `Shape` classe e può essere usata per creare linee. Per informazioni sulle proprietà `Line` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

`Line` definisce le proprietà seguenti:

- `X1`, di tipo Double, indica la coordinata x del punto iniziale della linea. Il valore predefinito di questa proprietà è 0,0.
- `Y1`, di tipo Double, indica la coordinata y del punto iniziale della linea. Il valore predefinito di questa proprietà è 0,0.
- `X2`, di tipo Double, indica la coordinata x del punto finale della linea. Il valore predefinito di questa proprietà è 0,0.
- `Y2`, di tipo Double, indica la coordinata y del punto finale della linea. Il valore predefinito di questa proprietà è 0,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Per informazioni sul controllo del modo in cui vengono disegnate le terminazioni della riga, vedere [controllo della riga](index.md#control-line-ends).

## <a name="create-a-line"></a>Crea una riga

Per creare una linea, creare un `Line` oggetto e impostare le `X1` relative `Y1` proprietà e sul punto iniziale e le relative `X2` `Y` proprietà e sul punto finale. Inoltre, impostare la `Stroke` proprietà su un oggetto [`Color`](xref:Xamarin.Forms.Color) perché una riga priva di tratto è invisibile.

> [!NOTE]
> L'impostazione della `Fill` proprietà di un oggetto `Line` non ha alcun effetto, perché una riga non ha interni.

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare una riga:

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="Red" />
```

In questo esempio viene disegnata una linea diagonale rossa da (40, 0) a (0120):

![Grafico a linee](line-images/line.png "Grafico a linee")

Poiché le `X1` `Y1` proprietà,, `X2` e `Y2` hanno valori predefiniti pari a 0, è possibile creare alcune linee con una sintassi minima:

```xaml
<Line Stroke="Red"
      X2="200" />
```

In questo esempio viene definita una linea orizzontale con 200 unità indipendenti dal dispositivo. Poiché le altre proprietà sono 0 per impostazione predefinita, viene disegnata una linea da (0,0) a (200, 0).

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare una linea tratteggiata:

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="DarkBlue"
      StrokeDashArray="1,1"
      StrokeDashOffset="6" />
```

In questo esempio viene disegnata una linea diagonale tratteggiata blu scuro da (40,0) a (0120):

![Linea tratteggiata](line-images/dashed-line.png "Linea tratteggiata")

Per altre informazioni su come disegnare una linea tratteggiata, vedere [disegnare forme tratteggiate](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsForme](index.md)
