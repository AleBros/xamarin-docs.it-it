---
title: Xamarin.FormsForme
description: Xamarin.FormsLe forme sono tipi di viste che consentono di creare forme sullo schermo.
ms.prod: xamarin
ms.assetid: 4E749FE8-852C-46DA-BB1E-652936106357
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1a1ba09b5be6901b1b07ed9aa3fd9a1b88770efc
ms.sourcegitcommit: 91b4d2f93687fadec5c3f80aadc8f7298d911624
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85795014"
---
# <a name="xamarinforms-shapes"></a>Xamarin.FormsForme

![](~/media/shared/preview.png "This API is currently pre-release")

Un `Shape` è un tipo di [`View`](xref:Xamarin.Forms.View) che consente di disegnare una forma sullo schermo. `Shape`gli oggetti possono essere utilizzati all'interno di classi di layout e la maggior parte dei controlli, perché la `Shape` classe deriva dalla `View` classe.

Xamarin.FormsLe forme sono disponibili nello `Xamarin.Forms.Shapes` spazio dei nomi in iOS, Android, MacOS, il piattaforma UWP (Universal Windows Platform) (UWP) e il Windows Presentation Foundation (WPF).

> [!IMPORTANT]
> Xamarin.FormsLe forme sono attualmente sperimentali e possono essere usate solo impostando il `Shapes_Experimental` flag. Per ulteriori informazioni, vedere [flag sperimentali](~/xamarin-forms/internals/experimental-flags.md).

`Shape` definisce le proprietà seguenti:

- `Aspect`, di tipo `Stretch` , descrive il modo in cui la forma riempie lo spazio allocato. Il valore predefinito di questa proprietà è `Stretch.None`.
- `Fill`, di tipo [`Color`](xref:Xamarin.Forms.Color) , indica il colore utilizzato per disegnare l'interno della forma.
- `Stroke`, di tipo [`Color`](xref:Xamarin.Forms.Color) , indica il colore utilizzato per disegnare il contorno della forma.
- `StrokeDashArray`, di tipo `DoubleCollection` , che rappresenta una raccolta di `double` valori che indicano il modello di trattini e spazi vuoti utilizzati per delineare una forma.
- `StrokeDashOffset`, di tipo `double` , specifica la distanza all'interno del modello di tratteggio in cui inizia un trattino. Il valore predefinito di questa proprietà è 0,0.
- `StrokeLineCap`, di tipo `PenLineCap` , descrive la forma all'inizio e alla fine di una linea o di un segmento. Il valore predefinito di questa proprietà è `PenLineCap.Flat`.
- `StrokeLineJoin`, di tipo `PenLineJoin` , specifica il tipo di join usato ai vertici di una forma. Il valore predefinito di questa proprietà è `PenLineJoin.Miter`.
- `StrokeThickness`, di tipo `double` , indica la larghezza del contorno della forma. Il valore predefinito di questa proprietà è 1,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Xamarin.Formsdefinisce un numero di oggetti che derivano dalla `Shape` classe. Sono `Ellipse` ,, `Line` , `Path` `Polygon` , `Polyline` e `Rectangle` .

## <a name="paint-shapes"></a>Forme disegno

[`Color`](xref:Xamarin.Forms.Color)gli oggetti vengono usati per disegnare le forme `Stroke` e `Fill` :

```xaml
<Ellipse Fill="DarkBlue"
         Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

In questo esempio vengono specificati il tratto e il riempimento di un oggetto `Ellipse` :

![Forme disegno](images/ellipse.png "Forme disegno")

> [!IMPORTANT]
> Se non si specifica un [`Color`](xref:Xamarin.Forms.Color) valore per `Stroke` oppure se si imposta `StrokeThickness` su 0, il bordo intorno alla forma non viene disegnato.

Per ulteriori informazioni sui [`Color`](xref:Xamarin.Forms.Color) valori validi, vedere [colori in Xamarin.Forms ](~/xamarin-forms/user-interface/colors.md).

## <a name="stretch-shapes"></a>Forme estesi

`Shape`gli oggetti hanno una `Aspect` proprietà di tipo `Stretch` . Questa proprietà determina il modo `Shape` in cui il contenuto di un oggetto viene allungato per riempire lo `Shape` spazio del layout dell'oggetto. Lo `Shape` spazio del layout di un oggetto corrisponde alla quantità di spazio `Shape` allocata dal Xamarin.Forms sistema di layout, a causa di un' `WidthRequest` impostazione esplicita e o a `HeightRequest` causa delle `HorizontalOptions` `VerticalOptions` Impostazioni e.

L'enumerazione `Stretch` definisce i membri seguenti:

- `None`, che indica che il contenuto conserva le dimensioni originali. Questo è il valore predefinito per la proprietà `Shape.Aspect`.
- `Fill`, che indica che il contenuto viene ridimensionato in modo da riempire le dimensioni di destinazione. Le proporzioni non vengono mantenute.
- `Uniform`, che indica che il contenuto viene ridimensionato in base alle dimensioni di destinazione, mantenendo le proporzioni.
- `UniformToFill`, indica che il contenuto viene ridimensionato in modo da riempire le dimensioni di destinazione, conservando le proporzioni. Se le proporzioni del rettangolo di destinazione sono diverse da quelle del contenuto di origine, questo viene ritagliato in base alle dimensioni di destinazione.

Nel codice XAML seguente viene illustrato come impostare la `Aspect` proprietà:

```xaml
<Path Aspect="Uniform"
      Stroke="Yellow"
      Fill="Red"
      BackgroundColor="LightGray"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100">
    <Path.Data>
        <!-- Path data goes here -->
    </Path.Data>  
</Path>      
```

In questo esempio un `Path` oggetto disegna un cuore. Le `Path` `WidthRequest` proprietà e dell'oggetto `HeightRequest` sono impostate su 100 unità indipendenti dal dispositivo e la relativa `Aspect` proprietà è impostata su `Uniform` . Di conseguenza, il contenuto dell'oggetto viene ridimensionato in base alle dimensioni di destinazione, conservando le proporzioni:

![Forme estesi](images/aspect.png "Forme estesi")

## <a name="draw-dashed-shapes"></a>Estrai forme tratteggiate

`Shape`gli oggetti hanno una `StrokeDashArray` proprietà di tipo `DoubleCollection` . Questa proprietà rappresenta una raccolta di `double` valori che indicano il modello di trattini e spazi vuoti utilizzati per delineare una forma. Un oggetto `DoubleCollection` è un oggetto `ObservableCollection` di `double` valori. Ogni `double` nella raccolta specifica la lunghezza di un trattino o di un gap. Il primo elemento della raccolta, che si trova in corrispondenza dell'indice 0, specifica la lunghezza di un trattino. Il secondo elemento della raccolta, che si trova in corrispondenza dell'indice 1, specifica la lunghezza di un gap. Gli oggetti con un valore di indice even specificano quindi trattini, mentre gli oggetti con un valore di indice dispari specificano gap.

`Shape`gli oggetti hanno anche una `StrokeDashOffset` proprietà, di tipo `double` , che specifica la distanza all'interno del modello di tratteggio in cui inizia un trattino. Se non si imposta questa proprietà, verrà generato `Shape` un contorno a tinta unita.

È possibile disegnare forme tratteggiate impostando le `StrokeDashArray` proprietà e `StrokeDashOffset` . La `StrokeDashArray` proprietà deve essere impostata su uno o più `double` valori, con ogni coppia delimitata da una singola virgola e/o uno o più spazi. Ad esempio, "0,5 1,0" e "0.5, 1.0" sono entrambi validi.

Nell'esempio di codice XAML riportato di seguito viene illustrato come creare un rettangolo tratteggiato:

```xaml
<Rectangle Fill="DarkBlue"
           Stroke="Red"
           StrokeThickness="4"
           StrokeDashArray="1,1"
           StrokeDashOffset="6"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

In questo esempio viene disegnato un rettangolo pieno con un tratto tratteggiato:

![Rettangolo tratteggiato](images/dashed-rectangle.png "Linea tratteggiata")

## <a name="control-line-ends"></a>Estremità della riga di controllo

Una riga è costituita da tre parti: Cap iniziale, corpo della linea e estremità finale. I tappi di inizio e di fine descrivono la forma all'inizio e alla fine di una linea o di un segmento.

`Shape`gli oggetti hanno una `StrokeLineCap` proprietà, di tipo `PenLineCap` , che descrive la forma all'inizio e alla fine di una linea o di un segmento. L'enumerazione `PenLineCap` definisce i membri seguenti:

- `Flat`, che rappresenta un limite che non si estende oltre l'ultimo punto della linea. Questo è paragonabile a nessun limite di riga e rappresenta il valore predefinito della `StrokeLineCap` Proprietà.
- `Square`, che rappresenta un rettangolo con un'altezza uguale allo spessore della linea e una lunghezza uguale a metà dello spessore della linea.
- `Round`, che rappresenta un semicerchio con un diametro uguale allo spessore della linea.

> [!IMPORTANT]
> La `StrokeLineCap` proprietà non ha alcun effetto se viene impostata su una forma senza punti di inizio o di fine. Questa proprietà, ad esempio, non ha alcun effetto se viene impostata su un oggetto `Ellipse` o su `Rectangle` .

Nel codice XAML seguente viene illustrato come impostare la `StrokeLineCap` proprietà:

```xaml
<Line X1="0"
      Y1="20"
      X2="300"
      Y2="20"
      StrokeLineCap="Round"
      Stroke="Red"
      StrokeThickness="12" />
```

In questo esempio, la linea rossa viene arrotondata all'inizio e alla fine della riga:

![Estremità di riga](images/linecap.png "Estremità di riga")

## <a name="control-line-joins"></a>Join di riga di controllo

`Shape`gli oggetti hanno una `StrokeLineJoin` proprietà, di tipo `PenLineJoin` , che specifica il tipo di join usato ai vertici della forma. L'enumerazione `PenLineJoin` definisce i membri seguenti:

- `Miter`, che rappresenta i vertici angolari regolari. Questo è il valore predefinito per la proprietà `StrokeLineJoin`.
- `Bevel`, che rappresenta i vertici smussati.
- `Round`, che rappresenta i vertici arrotondati.

Nel codice XAML seguente viene illustrato come impostare la `StrokeLineJoin` proprietà:

```xaml
<Polyline Points="20 20,250 50,20 120"
          Stroke="DarkBlue"
          StrokeThickness="20"
          StrokeLineJoin="Round" />
```

In questo esempio, la polilinea blu scuro presenta join arrotondati ai vertici:

![Join di riga](images/linejoin.png "Join di riga")

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Colori inXamarin.Forms](~/xamarin-forms/user-interface/colors.md)
