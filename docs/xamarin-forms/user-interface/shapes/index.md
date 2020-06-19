---
title: Xamarin.FormsForme
description: Xamarin.FormsLe forme sono tipi di viste che consentono di creare forme sullo schermo.
ms.prod: xamarin
ms.assetid: 4E749FE8-852C-46DA-BB1E-652936106357
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9c7c552abe724dca6b06265b73346a399d35c3cb
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101394"
---
# <a name="xamarinforms-shapes"></a>Xamarin.FormsForme

![](~/media/shared/preview.png "This API is currently pre-release")

Un `Shape` è un tipo di [`View`](xref:Xamarin.Forms.View) che consente di disegnare una forma sullo schermo. `Shape`gli oggetti possono essere utilizzati all'interno di classi di layout e la maggior parte dei controlli, perché la `Shape` classe deriva dalla `View` classe.

Xamarin.FormsLe forme sono disponibili nello `Xamarin.Forms.Shapes` spazio dei nomi in iOS, Android, MacOS, il piattaforma UWP (Universal Windows Platform) (UWP) e il Windows Presentation Foundation (WPF).

> [!IMPORTANT]
> Xamarin.FormsLe forme sono attualmente sperimentali e possono essere usate solo impostando il `Shapes_Experimental` flag. Per ulteriori informazioni, vedere [flag sperimentali](~/xamarin-forms/internals/experimental-flags.md).

`Shape` definisce le proprietà seguenti:

- `Aspect`, di tipo `Stretch` , descrive il modo in cui la forma riempie lo spazio allocato. Il valore predefinito di questa proprietà è `Stretch.None`.
- `Fill`, di tipo `Color` , indica il colore utilizzato per disegnare l'interno della forma.
- `Stroke`, di tipo `Color` , indica il colore utilizzato per disegnare il contorno della forma.
- `StrokeDashArray`, di tipo `DoubleCollection` , che rappresenta una raccolta di `double` valori che indicano il modello di trattini e spazi vuoti utilizzati per delineare una forma.
- `StrokeDashOffset`, di tipo `double` , specifica la distanza all'interno del modello di tratteggio in cui inizia un trattino. Il valore predefinito di questa proprietà è 0,0.
- `StrokeLineCap`, di tipo `PenLineCap` , descrive la forma all'inizio e alla fine di una linea o di un segmento. Il valore predefinito di questa proprietà è `PenLineCap.Flat`.
- `StrokeLineJoin`, di tipo `PenLineJoin` , specifica il tipo di join usato ai vertici di una forma. Il valore predefinito di questa proprietà è `PenLineJoin.Miter`.
- `StrokeThickness`, di tipo `double` , indica la larghezza del contorno della forma. Il valore predefinito di questa proprietà è 1,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Xamarin.Formsdefinisce un numero di oggetti che derivano dalla `Shape` classe. che includono `Ellipse`, `Line`, `Path`, `Polygon`, `Polyline` e `Rectangle`.

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
