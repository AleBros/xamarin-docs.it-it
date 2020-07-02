---
title: 'Xamarin.FormsForme: percorso'
description: La Xamarin.Forms classe Path può essere usata per creare curve e forme complesse.
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c0cd0e1939e443bc2c4a1ead8d0a29462ce04a68
ms.sourcegitcommit: 91b4d2f93687fadec5c3f80aadc8f7298d911624
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85795052"
---
# <a name="xamarinforms-shapes-path"></a>Xamarin.FormsForme: percorso

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scarica esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Path` classe deriva dalla `Shape` classe e può essere usata per creare curve e forme complesse. Queste curve e forme vengono spesso descritte utilizzando `Geometry` gli oggetti. Per informazioni sulle proprietà `Path` ereditate dalla classe dalla classe `Shape` , vedere [ Xamarin.Forms forme](index.md).

`Path` definisce le proprietà seguenti:

- `Data`, di tipo `Geometry` , che specifica la forma da disegnare.
- `RenderTransform`, di tipo `Transform` , che rappresenta la trasformazione applicata alla geometria di un tracciato prima che venga disegnata.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Per ulteriori informazioni sulle trasformazioni, vedere [ Xamarin.Forms trasformazioni del percorso](path-transforms.md).

## <a name="create-a-path"></a>Creazione di un percorso

Per tracciare un tracciato, creare un `Path` oggetto e impostarne la `Data` Proprietà. Sono disponibili due tecniche per l'impostazione della `Data` proprietà:

- È possibile impostare un valore stringa per `Data` in XAML, usando la sintassi di markup del percorso. Con questo approccio, il `Path.Data` valore utilizza un formato di serializzazione per la grafica. In genere, non si modifica questo valore di stringa manualmente dopo che è stato creato. È invece possibile usare gli strumenti di progettazione per modificare i dati ed esportarli come frammento di stringa che è possibile utilizzare con la `Data` Proprietà.
- È possibile impostare la `Data` proprietà su un `Geometry` oggetto. Può trattarsi di un `Geometry` oggetto specifico o di un oggetto `GeometryGroup` che funge da contenitore in grado di combinare più oggetti Geometry in un singolo oggetto.

### <a name="create-a-path-with-path-markup-syntax"></a>Creazione di un percorso con sintassi di markup del percorso

Nell'esempio di codice XAML seguente viene illustrato come creare un triangolo usando la sintassi di markup del percorso:

```xaml
<Path Data="M 10,100 L 100,100 100,50Z"
      Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Start" />
```

La `Data` stringa inizia con il comando Move, indicato da `M` , che stabilisce un punto iniziale assoluto per il percorso. `L`è il comando della riga, che crea una linea retta dal punto iniziale al punto finale specificato. `Z`è il comando Close, che crea una riga che connette il punto corrente al punto iniziale. Il risultato è un triangolo:

![Triangolo percorso](path-images/triangle.png "Triangolo percorso")

> [!NOTE]
> La sintassi di markup del percorso è disponibile solo in XAML.

Per ulteriori informazioni sulla sintassi di markup del percorso, vedere [ Xamarin.Forms sintassi di markup del percorso](path-markup-syntax.md).

### <a name="create-a-path-with-geometry-objects"></a>Creazione di un percorso con oggetti Geometry

Le curve e le forme possono essere descritte usando `Geometry` oggetti, che vengono usati per impostare la `Path` proprietà dell'oggetto `Data` . È possibile `Geometry` scegliere tra diversi oggetti. Le `EllipseGeometry` `LineGeometry` classi, e `RectangleGeometry` descrivono forme relativamente semplici. Per creare forme più complesse o creare curve, usare un oggetto `PathGeometry` .

`PathGeometry`gli oggetti sono costituiti da uno o più `PathFigure` oggetti. Ogni `PathFigure` oggetto rappresenta una forma diversa. Ogni `PathFigure` oggetto è costituito da uno o più `PathSegment` oggetti, ognuno dei quali rappresenta una parte di connessione della forma. I tipi di segmento includono le `LineSegment` seguenti `BezierSegment` classi, e `ArcSegment` .

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un triangolo utilizzando un `PathGeometry` oggetto:

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Start">
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

In questo esempio, il punto iniziale del triangolo è (10.100). Viene disegnato un segmento di linea da (10.100) a (100.100) e da (100.100) a (100, 50). Quindi il primo e l'ultimo segmento sono connessi, perché la `PathFigure.IsClosed` proprietà è impostata su `true` . Il risultato è un triangolo:

![Triangolo percorso](path-images/triangle.png "Triangolo percorso")

Per altre informazioni sulle geometrie, vedere [ Xamarin.Forms geometrie](geometries.md).

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsForme](index.md)
- [Xamarin.FormsGeometrie](geometries.md)
- [Xamarin.FormsSintassi di markup del percorso](path-markup-syntax.md)
- [Xamarin.FormsTrasformazioni percorso](path-transforms.md)
