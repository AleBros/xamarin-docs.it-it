---
title: Animazioni semplici inXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a4644094de9c0fcad8f38b7014426a30263dc66f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137449"
---
# <a name="simple-animations-in-xamarinforms"></a>Animazioni semplici inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_La classe ViewExtensions fornisce metodi di estensione che possono essere usati per costruire animazioni semplici. Questo articolo illustra la creazione e l'annullamento di animazioni con la classe ViewExtensions._

La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe fornisce i seguenti metodi di estensione che possono essere usati per creare animazioni semplici:

- [ `TranslateTo` ] (xrif: Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) aggiunge un'animazione alle [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) proprietà e di un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)aggiunge un'animazione alla [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- `ScaleXTo`aggiunge un'animazione alla [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) proprietà di un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- `ScaleYTo`aggiunge un'animazione alla [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) proprietà di un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RelScaleTo` ] (xrif: Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) applica un aumento incrementale animato o una riduzione alla [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) aggiunge un'animazione alla [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) proprietà di un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RelRotateTo` ] (xrif: Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) applica un aumento incrementale animato o una riduzione alla [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) proprietà di un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateXTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) aggiunge un'animazione alla [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) proprietà di un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateYTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) aggiunge un'animazione alla [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) proprietà di un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `FadeTo` ] (xrif: Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) aggiunge un'animazione alla [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) proprietà di un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

Per impostazione predefinita, per ogni animazione verranno necessari 250 millisecondi. Tuttavia, quando si crea l'animazione è possibile specificare una durata per ogni animazione.

La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe include anche [ `CancelAnimations` ] (xrif: Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . Metodo visualElement) che può essere usato per annullare qualsiasi animazione.

> [!NOTE]
> La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe fornisce un [ `LayoutTo` ] (xrif: Xamarin.Forms . ViewExtensions. LayoutTo ( Xamarin.Forms . Oggetti visivi, Xamarin.Forms . Rectangle, System. UInt32, Xamarin.Forms . Interpolazione)). Questo metodo, tuttavia, deve essere utilizzato dai layout per animare le transizioni tra gli Stati del layout che contengono le modifiche delle dimensioni e della posizione. Pertanto, deve essere utilizzato solo dalle [`Layout`](xref:Xamarin.Forms.Layout) sottoclassi.

I metodi di estensione dell'animazione nella [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe sono tutti asincroni e restituiscono un `Task<bool>` oggetto. Il valore restituito è `false` se l'animazione viene completata e `true` se l'animazione viene annullata. Pertanto, i metodi di animazione devono essere in genere utilizzati con l' `await` operatore, che consente di determinare facilmente quando un'animazione è stata completata. Inoltre, diventa possibile creare animazioni sequenziali con i metodi di animazione successivi eseguiti dopo il completamento del metodo precedente. Per altre informazioni, vedere [animazioni composte](#compound).

Se è necessario lasciare che un'animazione venga completata in background, l' `await` operatore può essere omesso. In questo scenario, i metodi di estensione dell'animazione restituiranno rapidamente dopo l'avvio dell'animazione, con l'animazione che si verifica in background. Questa operazione può essere sfruttata quando si creano animazioni composite. Per altre informazioni, vedere [animazioni composite](#composite).

Per ulteriori informazioni sull' `await` operatore, vedere [Cenni preliminari sul supporto asincrono](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animazioni singole

Ogni metodo di estensione in [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) implementa una singola operazione di animazione che modifica progressivamente una proprietà da un valore a un altro in un determinato periodo di tempo. In questa sezione vengono esaminate le singole operazioni di animazione.

### <a name="rotation"></a>Rotazione

Nell'esempio di codice seguente viene illustrato l'utilizzo di [ `RotateTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) per animare la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) proprietà di un oggetto [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Questo codice anima l' [`Image`](xref:Xamarin.Forms.Image) istanza ruotando fino a 360 gradi su 2 secondi (2000 millisecondi). [ `RotateTo` ] (Xrif: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) il metodo ottiene il [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) valore della proprietà corrente per l'inizio dell'animazione, quindi ruota da tale valore al primo argomento (360). Una volta completata l'animazione, la proprietà dell'immagine [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) viene reimpostata su 0. In questo modo si garantisce che la `Rotation` proprietà non rimanga a 360 dopo la conclusione dell'animazione, che impedisce ulteriori rotazioni.

Gli screenshot seguenti mostrano la rotazione in corso in ogni piattaforma:

![](simple-images/rotateto.png "Rotation Animation")

> [!NOTE]
> Oltre a [ `RotateTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)), sono presenti anche [ `RotateXTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione) e [ `RotateYTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) che animano [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) rispettivamente le proprietà e.

### <a name="relative-rotation"></a>Rotazione relativa

Nell'esempio di codice seguente viene illustrato l'utilizzo di [ `RelRotateTo` ] (xrif: Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) per aumentare o ridurre in modo incrementale la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) proprietà di un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RelRotateTo (360, 2000);
```

Questo codice anima l' [`Image`](xref:Xamarin.Forms.Image) istanza ruotando 360 gradi dalla posizione iniziale in 2 secondi (2000 millisecondi). [ `RelRotateTo` ] (Xrif: Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) il metodo ottiene il [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) valore della proprietà corrente per l'inizio dell'animazione, quindi ruota da tale valore al valore più il primo argomento (360). In questo modo si garantisce che ogni animazione sarà sempre una rotazione di 360 gradi dalla posizione iniziale. Se pertanto una nuova animazione viene richiamata mentre è già in corso un'animazione, viene avviata dalla posizione corrente e può terminare con una posizione che non è un incremento di 360 gradi.

Gli screenshot seguenti mostrano la rotazione relativa in corso in ogni piattaforma:

![](simple-images/relrotateto.png "Relative Rotation Animation")

### <a name="scaling"></a>Scalabilità

Nell'esempio di codice riportato di seguito viene illustrato l'utilizzo del [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) metodo per aggiungere un'animazione alla [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un oggetto [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.ScaleTo (2, 2000);
```

Questo codice anima l' [`Image`](xref:Xamarin.Forms.Image) istanza di scalando fino a due volte la dimensione superiore a 2 secondi (2000 millisecondi). Il [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) metodo ottiene il valore della [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà corrente (valore predefinito 1) per l'inizio dell'animazione, quindi viene scalato da tale valore al primo argomento (2). Questo ha l'effetto di espandere le dimensioni dell'immagine in base al doppio delle dimensioni.

Gli screenshot seguenti mostrano la scalabilità in corso in ogni piattaforma:

![](simple-images/scaleto.png "Scaling Animation")

> [!NOTE]
> Oltre al [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) metodo, sono disponibili anche i `ScaleXTo` metodi e `ScaleYTo` che animano rispettivamente le [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) proprietà e.

### <a name="relative-scaling"></a>Ridimensionamento relativo

Nell'esempio di codice seguente viene illustrato l'utilizzo di [ `RelScaleTo` ] (xrif: Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) per animare la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un oggetto [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RelScaleTo (2, 2000);
```

Questo codice anima l' [`Image`](xref:Xamarin.Forms.Image) istanza di scalando fino a due volte la dimensione superiore a 2 secondi (2000 millisecondi). [ `RelScaleTo` ] (Xrif: Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) il metodo ottiene il [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) valore della proprietà corrente per l'inizio dell'animazione, quindi viene scalato da tale valore al valore più il primo argomento (2). In questo modo si garantisce che ogni animazione sarà sempre un ridimensionamento di 2 dalla posizione iniziale.

### <a name="scaling-and-rotation-with-anchors"></a>Ridimensionamento e rotazione con ancoraggi

Le [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) proprietà e impostano il centro della scala o della rotazione per le [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà e. Pertanto, i rispettivi valori influiscono anche su [ `RotateTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) e [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) metodi.

Dato un oggetto [`Image`](xref:Xamarin.Forms.Image) che è stato inserito al centro di un layout, nell'esempio di codice seguente viene illustrata la rotazione dell'immagine intorno al centro del layout impostando la relativa [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) proprietà:

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

Per ruotare l' [`Image`](xref:Xamarin.Forms.Image) istanza intorno al centro del layout, le [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) proprietà e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) devono essere impostate su valori relativi alla larghezza e all'altezza di `Image` . In questo esempio, il centro di `Image` è definito come al centro del layout, quindi il `AnchorX` valore predefinito di 0,5 non richiede la modifica. Tuttavia, la `AnchorY` proprietà viene ridefinita in modo da essere un valore dalla parte superiore di `Image` al punto centrale del layout. In questo modo si garantisce che l'oggetto `Image` esegue una rotazione completa di 360 gradi intorno al punto centrale del layout, come illustrato nelle schermate seguenti:

![](simple-images/rotate-anchors.png "Rotation Animation with Anchors")

### <a name="translation"></a>Traduzione

Nell'esempio di codice seguente viene illustrato l'utilizzo di [ `TranslateTo` ] (xrif: Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) per animare [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) le [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) proprietà e di un oggetto [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Questo codice aggiunge un'animazione all' [`Image`](xref:Xamarin.Forms.Image) istanza convertendo l'oggetto orizzontalmente e verticalmente su 1 secondo (1000 millisecondi). [ `TranslateTo` ] (Xrif: Xamarin.Forms . ViewExtensions. TranslateTo ( Xamarin.Forms . VisualElement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) il metodo converte simultaneamente l'immagine 100 pixel a sinistra e 100 pixel verso l'alto. Questo è dovuto al fatto che il primo e il secondo argomento sono entrambi numeri negativi. Se si specificano numeri positivi, l'immagine verrà traslata a destra e in basso.

Gli screenshot seguenti mostrano la traduzione in corso in ogni piattaforma:

![](simple-images/translateto.png "Translation Animation")

> [!NOTE]
> Se un elemento viene inizialmente disposto allo schermo e quindi convertito sullo schermo, dopo la traduzione il layout di input dell'elemento rimane disattivato e l'utente non può interagire con esso. Pertanto, è consigliabile disporre di una vista nella posizione finale e quindi eseguire tutte le traduzioni richieste.

### <a name="fading"></a>Dissolvenza

Nell'esempio di codice seguente viene illustrato l'utilizzo di [ `FadeTo` ] (xrif: Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) per animare la [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) proprietà di un oggetto [`Image`](xref:Xamarin.Forms.Image) :

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Questo codice anima l' [`Image`](xref:Xamarin.Forms.Image) istanza dissolvendo il contenuto in più di 4 secondi (4000 millisecondi). [ `FadeTo` ] (Xrif: Xamarin.Forms . ViewExtensions. FadeTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) il metodo ottiene il [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) valore della proprietà corrente per l'inizio dell'animazione, quindi passa da tale valore al primo argomento (1).

Gli screenshot seguenti mostrano la dissolvenza in corso in ogni piattaforma:

![](simple-images/fadeto.png "Fading Animation")

<a name="compound" />

## <a name="compound-animations"></a>Animazioni composte

Un'animazione composta è una combinazione sequenziale di animazioni e può essere creata con l' `await` operatore, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

In questo esempio l'oggetto [`Image`](xref:Xamarin.Forms.Image) viene convertito in 6 secondi (6000 millisecondi). La traduzione di `Image` utilizza cinque animazioni, con l' `await` operatore che indica che ogni animazione viene eseguita in sequenza. Pertanto, i metodi di animazione successivi vengono eseguiti dopo il completamento del metodo precedente.

<a name="composite" />

## <a name="composite-animations"></a>Animazioni composite

Un'animazione composita è una combinazione di animazioni in cui due o più animazioni vengono eseguite simultaneamente. È possibile creare animazioni composite combinando animazioni attese e non attese, come illustrato nell'esempio di codice seguente:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

In questo esempio l'oggetto [`Image`](xref:Xamarin.Forms.Image) viene ridimensionato e ruotato simultaneamente in 4 secondi (4000 millisecondi). La scalabilità di `Image` Usa due animazioni sequenziali che si verificano contemporaneamente alla rotazione. [ `RotateTo` ] (Xrif: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) il metodo viene eseguito senza un `await` operatore e viene restituito immediatamente, con la prima [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) animazione a partire da. L' `await` operatore sulla prima `ScaleTo` chiamata al metodo ritarda la seconda chiamata al metodo `ScaleTo` fino al completamento della prima `ScaleTo` chiamata al metodo. A questo punto l' `RotateTo` animazione è stata completata a metà e l'oggetto `Image` verrà ruotato di 180 gradi. Durante i 2 secondi finali (2000 millisecondi), la seconda `ScaleTo` animazione e l' `RotateTo` animazione sono completate.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Esecuzione simultanea di più metodi asincroni

I `static` `Task.WhenAny` `Task.WhenAll` metodi e vengono usati per eseguire contemporaneamente più metodi asincroni e possono quindi essere usati per creare animazioni composite. Entrambi i metodi restituiscono un `Task` oggetto e accettano una raccolta di metodi che restituiscono un `Task` oggetto. Il `Task.WhenAny` metodo viene completato al completamento dell'esecuzione di un metodo della raccolta, come illustrato nell'esempio di codice seguente:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

In questo esempio, la `Task.WhenAny` chiamata al metodo contiene due attività. La prima attività ruota l'immagine su 4 secondi (4000 millisecondi) e la seconda attività ridimensiona l'immagine in 2 secondi (2000 millisecondi). Quando la seconda attività viene completata, la `Task.WhenAny` chiamata al metodo viene completata. Tuttavia, anche se [ `RotateTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione)) il metodo è ancora in esecuzione, il secondo [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) metodo può iniziare.

Il `Task.WhenAll` metodo viene completato quando tutti i metodi nella relativa raccolta sono stati completati, come illustrato nell'esempio di codice seguente:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

In questo esempio, la `Task.WhenAll` chiamata al metodo contiene tre attività, ognuna delle quali viene eseguita in 10 minuti. Ogni `Task` Crea un numero diverso di rotazioni di 360 gradi, ovvero 307 rotazioni per [ `RotateTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione), 251 rotazioni per [ `RotateXTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione) e 199 rotazioni per [ `RotateYTo` ] (xrif: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Interpolazione). Questi valori sono numeri primi, assicurando quindi che le rotazioni non siano sincronizzate e pertanto non comporteranno modelli ripetitivi.

Gli screenshot seguenti mostrano le rotazioni multiple in corso in ogni piattaforma:

![](simple-images/multiple-rotations.png "Composite Animation")

## <a name="canceling-animations"></a>Annullamento di animazioni

Un'applicazione può annullare una o più animazioni con una chiamata a `static` [ `ViewExtensions.CancelAnimations` ] (xrif: Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . Metodo visualElement), come illustrato nell'esempio di codice seguente:

```csharp
ViewExtensions.CancelAnimations (image);
```

Verranno annullate immediatamente tutte le animazioni attualmente in esecuzione nell' [`Image`](xref:Xamarin.Forms.Image) istanza.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare e annullare animazioni usando la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe. Questa classe fornisce metodi di estensione che possono essere usati per costruire animazioni semplici che ruotano, ridimensionano, traducono e sfumano le [`VisualElement`](xref:Xamarin.Forms.VisualElement) istanze.

## <a name="related-links"></a>Collegamenti correlati

- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
- [Animazione di base (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
