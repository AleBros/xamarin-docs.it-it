---
title: 'Xamarin.FormsForme: percorso'
description: La Xamarin.Forms classe Path può essere usata per creare curve e forme complesse.
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cb2e3ea68f181f0a6e6392c012db221ef35d65bd
ms.sourcegitcommit: 16847681df17ed59b3b3528761c02e8fb48ffc4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85104301"
---
# <a name="xamarinforms-shapes-path"></a>Xamarin.FormsForme: percorso

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Path` classe deriva dalla `Shape` classe e può essere usata per creare curve e forme complesse. Queste curve e forme vengono spesso descritte utilizzando `Geometry` gli oggetti. Per informazioni sulle proprietà `Path` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

`Path` definisce le proprietà seguenti:

- `Data`, di tipo `Geometry` , che specifica la forma da disegnare.
- `RenderTransform`, di tipo `Transform` , che rappresenta la trasformazione applicata alla geometria di un tracciato prima che venga disegnata.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Per ulteriori informazioni sulle trasformazioni, vedere [ Xamarin.Forms trasformazioni del percorso](path-transforms.md).

## <a name="create-a-path"></a>Creazione di un percorso

Nell'esempio di codice XAML seguente viene illustrato come creare un poligono usando una sintassi abbreviata speciale nota come sintassi di markup del percorso:

```xaml
<Path Data="M 10,50 L 200,70"
      Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100" />
```

La `Data` stringa inizia con il comando "MoveTo", indicato da `M` , che stabilisce un punto iniziale per il percorso. `L`è il comando della riga, che crea una linea retta dal punto iniziale al punto finale specificato.

> [!NOTE]
> La sintassi di markup del percorso è disponibile solo in XAML.

Per ulteriori informazioni sulla sintassi di markup del percorso, vedere [ Xamarin.Forms sintassi di markup del percorso](path-markup-syntax.md).

## <a name="path-geometry"></a>Geometria percorso

Le curve e le forme possono essere descritte usando `Geometry` oggetti, che vengono usati per impostare la `Path` proprietà dell'oggetto `Data` .

È possibile `Geometry` scegliere tra diversi oggetti. Le `EllipseGeometry` `LineGeometry` classi, e `RectangleGeometry` descrivono forme relativamente semplici. Per creare forme più complesse o creare curve, usare un oggetto `PathGeometry` .

`PathGeometry`gli oggetti sono costituiti da uno o più `PathFigure` oggetti. Ogni `PathFigure` oggetto rappresenta una forma diversa. Ogni `PathFigure` oggetto è costituito da uno o più `PathSegment` oggetti, ognuno dei quali rappresenta una parte di connessione della forma. I tipi di segmenti includono i seguenti: `LineSegment` , `BezierSegment` e `ArcSegment` .

Nell'esempio seguente `Path` viene usato un oggetto per creare un triangolo:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure IsClosed="True"
                                StartPoint="10,100">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="100,100" />
                                <LineSegment Point="100,50" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Per altre informazioni sulle geometrie, vedere [ Xamarin.Forms geometrie](geometries.md).

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsForme](index.md)
- [Xamarin.FormsGeometrie](geometries.md)
- [Xamarin.FormsSintassi di markup del percorso](path-markup-syntax.md)
- [Xamarin.FormsTrasformazioni percorso](path-transforms.md)
