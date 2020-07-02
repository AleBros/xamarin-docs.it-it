---
title: 'Xamarin.FormsForme: trasformazioni del percorso'
description: Una Xamarin.Forms trasformazione definisce la modalità di trasformazione di un oggetto Path da uno spazio delle coordinate a un altro spazio delle coordinate.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 41de95c452212dce77d6365265e4813170c9b9b9
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853047"
---
# <a name="xamarinforms-shapes-path-transforms"></a>Xamarin.FormsForme: trasformazioni del percorso

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scarica esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Un `Transform` oggetto definisce la modalità di trasformazione di un `Path` oggetto da uno spazio delle coordinate a un altro spazio delle coordinate. Quando una trasformazione viene applicata a un `Path` oggetto, modifica la modalità di rendering dell'oggetto nell'interfaccia utente.

Le trasformazioni possono essere categorizzate in quattro classificazioni generali: rotazione, scala, asimmetria e conversione. Xamarin.Formsdefinisce una classe per ognuna di queste classificazioni di trasformazione:

- `RotateTransform`, che ruota un oggetto `Path` in base a un oggetto specificato `Angle` .
- `ScaleTransform`, che consente di ridimensionare un `Path` oggetto in base agli `ScaleX` `ScaleY` importi specificati.
- `SkewTransform`, che inclina un `Path` oggetto in base agli `AngleX` `AngleY` importi specificati.
- `TranslateTransform`, che sposta un `Path` oggetto in base `X` agli `Y` importi specificati.

Xamarin.Formsin sono inoltre disponibili le classi seguenti per la creazione di trasformazioni più complesse:

- `TransformGroup`, che rappresenta una trasformazione composita composta da più oggetti Transform.
- `CompositeTransform`, che applica più operazioni di trasformazione a un `Path` oggetto.
- `MatrixTransform`, che consente di creare trasformazioni personalizzate non fornite dalle altre classi Transform.

Tutte queste classi derivano dalla `Transform` classe, che definisce una `Value` proprietà di tipo `Matrix` . Questa proprietà rappresenta la trasformazione corrente come `Matrix` oggetto. Per ulteriori informazioni sullo `Matrix` struct, vedere [transform matrix](#transform-matrix).

Per applicare una trasformazione a un oggetto `Path` , è necessario creare una classe Transform e impostarla come valore della `Path.RenderTransform` Proprietà.

## <a name="rotation-transform"></a>Trasformazione rotazione

Una trasformazione rotazione ruota un `Path` oggetto in senso orario intorno a un punto specificato in un sistema di coordinate x-y 2D.

La `RotateTransform` classe, che deriva dalla `Transform` classe, definisce le proprietà seguenti:

- `Angle`, di tipo `double` , rappresenta l'angolo, in gradi, della rotazione in senso orario. Il valore predefinito di questa proprietà è 0,0.
- `CenterX`, di tipo `double` , rappresenta la coordinata x del punto centrale della rotazione. Il valore predefinito di questa proprietà è 0,0.
- `CenterY`, di tipo `double` , rappresenta la coordinata y del punto centrale della rotazione. Il valore predefinito di questa proprietà è 0,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Le `CenterX` `CenterY` proprietà e specificano il punto in cui l' `Path` oggetto viene ruotato. Questo punto centrale è espresso nello spazio delle coordinate dell'oggetto trasformato. Per impostazione predefinita, la rotazione viene applicata a (0,0), ovvero l'angolo superiore sinistro dell' `Path` oggetto.

Nell'esempio seguente viene illustrato come ruotare un `Path` oggetto:

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <RotateTransform CenterX="0"
                         CenterY="0"
                         Angle="45" />
    </Path.RenderTransform>
</Path>
```

In questo esempio, l' `Path` oggetto viene ruotato di 45 gradi sull'angolo superiore sinistro.

## <a name="scale-transform"></a>Ridimensiona trasformazione

Una trasformazione scala ridimensiona un `Path` oggetto nel sistema di coordinate x-y 2D.

La `ScaleTransform` classe, che deriva dalla `Transform` classe, definisce le proprietà seguenti:

- `ScaleX`, di tipo `double` , che rappresenta il fattore di scala dell'asse x. Il valore predefinito di questa proprietà è 1,0.
- `ScaleY`, di tipo `double` , che rappresenta il fattore di scala dell'asse y. Il valore predefinito di questa proprietà è 1,0.
- `CenterX`, di tipo `double` , che rappresenta la coordinata x del punto centrale della trasformazione. Il valore predefinito di questa proprietà è 0,0.
- `CenterY`, di tipo `double` , che rappresenta la coordinata y del punto centrale della trasformazione. Il valore predefinito di questa proprietà è 0,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Il valore di `ScaleX` e `ScaleY` ha un notevole effetto sul ridimensionamento risultante:

- I valori compresi tra 0 e 1 riducono la larghezza e l'altezza dell'oggetto ridimensionato.
- I valori maggiori di 1 aumentano la larghezza e l'altezza dell'oggetto ridimensionato.
- I valori 1 indicano che l'oggetto non viene ridimensionato.
- I valori negativi capovolgere l'oggetto scala orizzontalmente e verticalmente.
- Valori compresi tra 0 e-1 Capovolgere l'oggetto scala e ridurne la larghezza e l'altezza.
- I valori minori di-1 capovolgeno l'oggetto e ne aumentano la larghezza e l'altezza.
- I valori di-1 comprimono l'oggetto ridimensionato, ma non ne cambiano le dimensioni orizzontali o verticali.

Le `CenterX` `CenterY` proprietà e specificano il punto di `Path` ridimensionamento dell'oggetto. Questo punto centrale è espresso nello spazio delle coordinate dell'oggetto trasformato. Per impostazione predefinita, il ridimensionamento viene applicato a (0,0), ovvero l'angolo superiore sinistro dell' `Path` oggetto. Questo ha l'effetto di trasferire l' `Path` oggetto e renderlo più grande, perché quando si applica una trasformazione si modifica lo spazio delle coordinate in cui si `Path` trova l'oggetto.

Nell'esempio seguente viene illustrato come ridimensionare un `Path` oggetto:

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <ScaleTransform CenterX="0"
                        CenterY="0"
                        ScaleX="1.5"
                        ScaleY="1.5" />
    </Path.RenderTransform>
</Path>
```

In questo esempio, l' `Path` oggetto viene ridimensionato a 1,5 volte la dimensione.

## <a name="skew-transform"></a>Trasformazione inclinazione

Una trasformazione asimmetria inclina un `Path` oggetto nel sistema di coordinate x-y 2D ed è utile per creare l'illusione della profondità 3D in un oggetto 2D.

La `SkewTransform` classe, che deriva dalla `Transform` classe, definisce le proprietà seguenti:

- `AngleX`, di tipo `double` , che rappresenta l'angolo di inclinazione dell'asse x, misurato in gradi in senso antiorario rispetto all'asse y. Il valore predefinito di questa proprietà è 0,0.
- `AngleY`, di tipo `double` , che rappresenta l'angolo di inclinazione dell'asse y, misurato in gradi in senso antiorario rispetto all'asse x. Il valore predefinito di questa proprietà è 0,0.
- `CenterX`, di tipo `double` , che rappresenta la coordinata x del centro della trasformazione. Il valore predefinito di questa proprietà è 0,0.
- `CenterY`, di tipo `double` , che rappresenta la coordinata y del centro della trasformazione. Il valore predefinito di questa proprietà è 0,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Per stimare l'effetto di una trasformazione asimmetria, tenere presente che `AngleX` inclina i valori dell'asse x rispetto al sistema di coordinate originale. Pertanto, per un valore `AngleX` pari a 30, l'asse y ruota di 30 gradi attraverso l'origine e inclina i valori in x di 30 gradi da tale origine. Analogamente, un `AngleY` valore pari a 30 inclina i valori y dell' `Path` oggetto di 30 gradi dall'origine.

> [!NOTE]
> Per inclinare un `Path` oggetto sul posto, impostare le `CenterX` proprietà e sul `CenterY` punto centrale dell'oggetto.

Nell'esempio seguente viene illustrato come inclinare un `Path` oggetto:

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <SkewTransform CenterX="0"
                       CenterY="0"
                       AngleX="45"
                       AngleY="0" />
    </Path.RenderTransform>
</Path>
```

In questo esempio viene applicata un'inclinazione orizzontale di 45 gradi all' `Path` oggetto, da un punto centrale di (0, 0).

## <a name="translate-transform"></a>Trasla trasformazione

Una trasformazione translate sposta un oggetto nel sistema di coordinate x-y 2D.

La `TranslateTransform` classe, che deriva dalla `Transform` classe, definisce le proprietà seguenti:

- `X`, di tipo `double` , che rappresenta la distanza di spostamento lungo l'asse x. Il valore predefinito di questa proprietà è 0,0.
- `Y`, di tipo `double` , che rappresenta la distanza di spostamento lungo l'asse y. Il valore predefinito di questa proprietà è 0,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

`X`I valori negativi spostano un oggetto a sinistra, mentre i valori positivi spostano un oggetto a destra. `Y`I valori negativi spostano un oggetto verso l'alto, mentre i valori positivi spostano un oggetto verso il basso.

Nell'esempio seguente viene illustrato come convertire un `Path` oggetto:

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TranslateTransform X="50"
                            Y="50" />
    </Path.RenderTransform>
</Path>
```

In questo esempio, l' `Path` oggetto viene spostato 50 unità indipendenti dal dispositivo a destra e 50 unità indipendenti dal dispositivo.

## <a name="multiple-transforms"></a>Più trasformazioni

Xamarin.Formsdispone di due classi che supportano l'applicazione di più trasformazioni a un `Path` oggetto. Sono `TransformGroup` , e `CompositeTransform` . Un oggetto `TransformGroup` esegue le trasformazioni in qualsiasi ordine desiderato, mentre un oggetto `CompositeTransform` esegue le trasformazioni in un ordine specifico.

### <a name="transform-groups"></a>Gruppi di trasformazione

I gruppi di trasformazione rappresentano trasformazioni composite composte da più `Transform` oggetti.

La `TransformGroup` classe, che deriva dalla `Transform` classe, definisce una `Children` proprietà di tipo `TransformCollection` , che rappresenta una raccolta di `Transform` oggetti. Questa proprietà è supportata da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che può essere la destinazione di data binding e stile.

L'ordine delle trasformazioni è importante in una trasformazione composita che usa la `TransformGroup` classe. Se, ad esempio, si esegue prima la rotazione, quindi si esegue la scalabilità e si traduce, si ottiene un risultato diverso da quello in cui si esegue la prima conversione, quindi si ruota e quindi si ridimensiona. Un ordine è significativo perché le trasformazioni come la rotazione e la scalabilità vengono eseguite rispetto all'origine del sistema di coordinate. Il ridimensionamento di un oggetto centrato sull'origine produce un risultato diverso per la scalabilità di un oggetto che è stato rimosso dall'origine. Analogamente, la rotazione di un oggetto centrato nell'origine produce un risultato diverso rispetto alla rotazione di un oggetto che è stato rimosso dall'origine.

Nell'esempio seguente viene illustrato come eseguire una trasformazione composita utilizzando la `TransformGroup` classe:

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TransformGroup>
            <ScaleTransform ScaleX="1.5"
                            ScaleY="1.5" />
            <RotateTransform Angle="45" />
        </TransformGroup>
    </Path.RenderTransform>
</Path>
```

In questo esempio, l' `Path` oggetto viene ridimensionato a 1,5 volte le sue dimensioni e quindi ruotato di 45 gradi.

## <a name="composite-transforms"></a>Trasformazioni composite

Una trasformazione composita applica più trasformazioni a un oggetto.

La `CompositeTransform` classe, che deriva dalla `Transform` classe, definisce le proprietà seguenti:

- `CenterX`, di tipo `double` , che rappresenta la coordinata x del punto centrale della trasformazione. Il valore predefinito di questa proprietà è 0,0.
- `CenterY`, di tipo `double` , che rappresenta la coordinata y del punto centrale della trasformazione. Il valore predefinito di questa proprietà è 0,0.
- `ScaleX`, di tipo `double` , che rappresenta il fattore di scala dell'asse x. Il valore predefinito di questa proprietà è 1,0.
- `ScaleY`, di tipo `double` , che rappresenta il fattore di scala dell'asse y. Il valore predefinito di questa proprietà è 1,0.
- `SkewX`, di tipo `double` , che rappresenta l'angolo di inclinazione dell'asse x, misurato in gradi in senso antiorario rispetto all'asse y. Il valore predefinito di questa proprietà è 0,0.
- `SkewY`, di tipo `double` , che rappresenta l'angolo di inclinazione dell'asse y, misurato in gradi in senso antiorario rispetto all'asse x. Il valore predefinito di questa proprietà è 0,0.
- `Rotation`, di tipo `double` , rappresenta l'angolo, in gradi, della rotazione in senso orario. Il valore predefinito di questa proprietà è 0,0.
- `TranslateX`, di tipo `double` , che rappresenta la distanza di spostamento lungo l'asse x. Il valore predefinito di questa proprietà è 0,0.
- `TranslateY`, di tipo `double` , che rappresenta la distanza di spostamento lungo l'asse y. Il valore predefinito di questa proprietà è 0,0.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

Un `CompositeTransform` applica le trasformazioni nell'ordine seguente:

1. Scala ( `ScaleX` e `ScaleY` ).
1. Inclinazione ( `SkewX` e `SkewY` ).
1. Ruotare ( `Rotation` ).
1. Translate ( `TranslateX` , `TranslateY` ).

Se si desidera applicare più trasformazioni a un oggetto in un ordine diverso, è necessario creare un `TransformGroup` e inserire le trasformazioni nell'ordine previsto.

> [!IMPORTANT]
> Un oggetto `CompositeTransform` utilizza gli stessi punti centrali `CenterX` e `CenterY` , per tutte le trasformazioni. Se si desidera specificare punti centrali diversi per ogni trasformazione, utilizzare un `TransformGroup` ,

Nell'esempio seguente viene illustrato come eseguire una trasformazione composita utilizzando la `CompositeTransform` classe:

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <CompositeTransform ScaleX="1.5"
                            ScaleY="1.5"
                            Rotation="45"
                            TranslateX="50"
                            TranslateY="50" />
    </Path.RenderTransform>
</Path>
```

In questo esempio, l' `Path` oggetto viene ridimensionato a 1,5 volte le sue dimensioni, quindi ruotato di 45 gradi e quindi convertito da 50 unità indipendenti dal dispositivo.

## <a name="transform-matrix"></a>Trasforma matrice

Una trasformazione può essere descritta in termini di una matrice di trasformazione affine 3x3 che esegue trasformazioni nello spazio 2D. Questa matrice 3x3 è rappresentata dallo `Matrix` struct, ovvero una raccolta di tre righe e tre colonne di `double` valori.

Lo `Matrix` struct definisce le proprietà seguenti:

- `Determinant`, di tipo `double` , che ottiene il determinante della matrice.
- `HasInverse`, di tipo `bool` , che indica se la matrice è invertibile.
- `Identity`, di tipo `Matrix` , che ottiene una matrice di identità.
- `HasIdentity`, di tipo `bool` , che indica se la matrice è una matrice di identità.
- `M11`, di tipo `double` , che rappresenta il valore della prima riga e della prima colonna della matrice.
- `M12`, di tipo `double` , che rappresenta il valore della prima riga e della seconda colonna della matrice.
- `M21`, di tipo `double` , che rappresenta il valore della seconda riga e della prima colonna della matrice.
- `M22`, di tipo `double` , che rappresenta il valore della seconda riga e della seconda colonna della matrice.
- `OffsetX`, di tipo `double` , che rappresenta il valore della terza riga e della prima colonna della matrice.
- `OffsetY`, di tipo `double` , che rappresenta il valore della terza riga e della seconda colonna della matrice.

Le `OffsetX` `OffsetY` proprietà e sono denominate in quanto specificano la quantità per convertire rispettivamente lo spazio delle coordinate lungo l'asse x e l'asse y.

Inoltre, lo `Matrix` struct espone una serie di metodi che possono essere utilizzati per modificare i valori della matrice, tra cui `Append` ,, `Invert` `Multiply` `Prepend` e molti altri.

Nella tabella seguente viene illustrata la struttura di una Xamarin.Forms matrice:

| | | |
|---------|---------|-----|
| M11     | M12     | 0,0 |
| M21     | M22     | 0,0 |
| OffsetX | OffsetY | 1.0 |

> [!NOTE]
> Una matrice di trasformazione affine ha la colonna finale uguale a (0, 0, 1), pertanto è necessario specificare solo i membri nelle prime due colonne.

Manipolando i valori della matrice, è possibile ruotare, ridimensionare, inclinare e tradurre `Path` oggetti. Se ad esempio si modifica il `OffsetX` valore in 100, è possibile usarlo per spostare un `Path` oggetto 100 unità indipendenti dal dispositivo lungo l'asse x. Se si modifica il `M22` valore in 3, è possibile usarlo per estendere un `Path` oggetto a tre volte l'altezza corrente. Se si modificano entrambi i valori, spostare l' `Path` oggetto 100 unità indipendenti dal dispositivo lungo l'asse x e allungare l'altezza di un fattore 3. Inoltre, le matrici di trasformazione affini possono essere moltiplicate per formare un numero qualsiasi di trasformazioni lineari, ad esempio rotazione e asimmetria, seguite dalla conversione.

## <a name="custom-transforms"></a>Trasformazioni personalizzate

La `MatrixTransform` classe, che deriva dalla `Transform` classe, definisce una `Matrix` proprietà di tipo che `Matrix` rappresenta la matrice che definisce la trasformazione. Questa proprietà è supportata da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto, il che significa che può essere la destinazione di data binding e stile.

Qualsiasi trasformazione che è possibile descrivere con un `TranslateTransform` `ScaleTransform` oggetto,, `RotateTransform` o `SkewTransform` può essere descritta ugualmente da un oggetto `MatrixTransform` . Tuttavia, le `TranslateTransform` `ScaleTransform` classi,, `RotateTransform` e `SkewTransform` sono più facili da concettualizzare rispetto all'impostazione dei componenti vettoriali in un oggetto `Matrix` . Pertanto, la `MatrixTransform` classe viene in genere utilizzata per creare trasformazioni personalizzate che non sono fornite `RotateTransform` dalle `ScaleTransform` classi,, `SkewTransform` o `TranslateTransform` .

Nell'esempio seguente viene illustrato come trasformare un `Path` oggetto utilizzando `MatrixTransform` :

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform>
            <MatrixTransform.Matrix>
                <!-- M11 stretches, M12 skews -->
                <Matrix OffsetX="10"
                        OffsetY="100"
                        M11="1.5"
                        M12="1" />
            </MatrixTransform.Matrix>
        </MatrixTransform>
    </Path.RenderTransform>
</Path>
```

In questo esempio l' `Path` oggetto è allungato, inclinato e offset nelle dimensioni X e Y.

In alternativa, può essere scritto in un formato semplificato che usa un convertitore di tipi incorporato in Xamarin.Forms :

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform Matrix="1.5,1,0,1,10,100" />
    </Path.RenderTransform>
</Path>
```

In questo esempio, la `Matrix` proprietà viene specificata come stringa delimitata da virgole costituita da sei membri: `M11` , `M12` , `M21` , `M22` , `OffsetX` , `OffsetY` . Mentre i membri sono delimitati da virgole in questo esempio, possono anche essere delimitati da uno o più spazi.

Inoltre, l'esempio precedente può essere ulteriormente semplificato, specificando gli stessi sei membri del valore della `RenderTransform` proprietà:

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      RenderTransform="1.5 1 0 1 10 100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z" />
```

## <a name="related-links"></a>Collegamenti correlati

- [ShapeDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsForme](index.md)
