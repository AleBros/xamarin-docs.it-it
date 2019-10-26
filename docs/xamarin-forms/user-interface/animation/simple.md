---
title: Animazioni semplici in Novell. Forms
description: La classe ViewExtensions fornisce metodi di estensione che possono essere usati per costruire animazioni semplici. Questo articolo illustra la creazione e l'annullamento di animazioni con la classe ViewExtensions.
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2019
ms.openlocfilehash: 116911787db128b103fb555554076704a0549db5
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959176"
---
# <a name="simple-animations-in-xamarinforms"></a>Animazioni semplici in Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_La classe ViewExtensions fornisce metodi di estensione che possono essere usati per costruire animazioni semplici. Questo articolo illustra la creazione e l'annullamento di animazioni con la classe ViewExtensions._

La classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) fornisce i metodi di estensione seguenti che possono essere usati per creare animazioni semplici:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) aggiunge un'animazione alle proprietà [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) di una [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) aggiunge un'animazione alla proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) di un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) applica un aumento o una riduzione incrementale dell'animazione alla proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) di un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) aggiunge un'animazione alla proprietà [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) di un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) applica un aumento o una riduzione incrementale dell'animazione alla proprietà [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) di un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) aggiunge un'animazione alla proprietà [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) di un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) aggiunge un'animazione alla proprietà [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) di un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) aggiunge un'animazione alla proprietà [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) di un [`VisualElement`](xref:Xamarin.Forms.VisualElement).

Per impostazione predefinita, per ogni animazione verranno necessari 250 millisecondi. Tuttavia, quando si crea l'animazione è possibile specificare una durata per ogni animazione.

La classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) include anche un metodo [`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) che può essere usato per annullare qualsiasi animazione.

> [!NOTE]
> La classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) fornisce un [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)) metodo di estensione. Questo metodo, tuttavia, deve essere utilizzato dai layout per animare le transizioni tra gli Stati del layout che contengono le modifiche delle dimensioni e della posizione. Pertanto, deve essere utilizzato solo dalle sottoclassi [`Layout`](xref:Xamarin.Forms.Layout) .

I metodi di estensione dell'animazione nella classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) sono tutti asincroni e restituiscono un oggetto `Task<bool>`. Il valore restituito è `false` se l'animazione viene completata e `true` se l'animazione viene annullata. Pertanto, i metodi di animazione devono essere in genere utilizzati con l'operatore `await`, che consente di determinare facilmente quando un'animazione è stata completata. Inoltre, diventa possibile creare animazioni sequenziali con i metodi di animazione successivi eseguiti dopo il completamento del metodo precedente. Per altre informazioni, vedere [animazioni composte](#compound).

Se è necessario lasciare che un'animazione venga completata in background, è possibile omettere l'operatore `await`. In questo scenario, i metodi di estensione dell'animazione restituiranno rapidamente dopo l'avvio dell'animazione, con l'animazione che si verifica in background. Questa operazione può essere sfruttata quando si creano animazioni composite. Per altre informazioni, vedere [animazioni composite](#composite).

Per ulteriori informazioni sull'operatore `await`, vedere [Cenni preliminari sul supporto asincrono](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animazioni singole

Ogni metodo di estensione nel [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) implementa una singola operazione di animazione che modifica progressivamente una proprietà da un valore a un altro in un determinato periodo di tempo. In questa sezione vengono esaminate le singole operazioni di animazione.

### <a name="rotation"></a>Rotazione

Nell'esempio di codice riportato di seguito viene illustrato l'utilizzo del metodo [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) per aggiungere un'animazione alla proprietà [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) di un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Questo codice anima l'istanza di [`Image`](xref:Xamarin.Forms.Image) ruotando fino a 360 gradi in 2 secondi (2000 millisecondi). Il metodo [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) ottiene il valore corrente della proprietà [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) per l'inizio dell'animazione, quindi ruota da tale valore al primo argomento (360). Una volta completata l'animazione, la proprietà [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) dell'immagine viene reimpostata su 0. In questo modo si garantisce che la proprietà `Rotation` non rimanga a 360 dopo la conclusione dell'animazione, che impedisce ulteriori rotazioni.

Gli screenshot seguenti mostrano la rotazione in corso in ogni piattaforma:

![](simple-images/rotateto.png "Rotation Animation")

### <a name="relative-rotation"></a>Rotazione relativa

Nell'esempio di codice riportato di seguito viene illustrato l'utilizzo del metodo [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) per aumentare o diminuire in modo incrementale la proprietà [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) di un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RelRotateTo (360, 2000);
```

Questo codice anima l'istanza di [`Image`](xref:Xamarin.Forms.Image) ruotando 360 gradi dalla posizione iniziale in 2 secondi (2000 millisecondi). Il metodo [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) ottiene il valore corrente della proprietà [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) per l'inizio dell'animazione, quindi ruota da tale valore al valore più il primo argomento (360). In questo modo si garantisce che ogni animazione sarà sempre una rotazione di 360 gradi dalla posizione iniziale. Se pertanto una nuova animazione viene richiamata mentre è già in corso un'animazione, viene avviata dalla posizione corrente e può terminare con una posizione che non è un incremento di 360 gradi.

Gli screenshot seguenti mostrano la rotazione relativa in corso in ogni piattaforma:

![](simple-images/relrotateto.png "Relative Rotation Animation")

### <a name="scaling"></a>Ridimensionamento

Nell'esempio di codice riportato di seguito viene illustrato l'utilizzo del metodo [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) per aggiungere un'animazione alla proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) di un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.ScaleTo (2, 2000);
```

Questo codice anima l'istanza di [`Image`](xref:Xamarin.Forms.Image) scalando fino a due volte la dimensione superiore a 2 secondi (2000 millisecondi). Il metodo [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) ottiene il valore corrente della proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) (valore predefinito 1) per l'inizio dell'animazione, quindi viene scalato da tale valore al primo argomento (2). Questo ha l'effetto di espandere le dimensioni dell'immagine in base al doppio delle dimensioni.

Gli screenshot seguenti mostrano la scalabilità in corso in ogni piattaforma:

![](simple-images/scaleto.png "Scaling Animation")

> [!NOTE]
> La classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) definisce anche le proprietà [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) e [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY), che possono modificare in scala `VisualElement` in modi diversi in direzione orizzontale e verticale. Queste proprietà possono essere animate con la classe [`Animation`](xref:Xamarin.Forms.Animation) . Per altre informazioni, vedere [animazioni personalizzate in Novell. Forms](custom.md).

### <a name="relative-scaling"></a>Ridimensionamento relativo

Nell'esempio di codice riportato di seguito viene illustrato l'utilizzo del metodo [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) per aggiungere un'animazione alla proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) di un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RelScaleTo (2, 2000);
```

Questo codice anima l'istanza di [`Image`](xref:Xamarin.Forms.Image) scalando fino a due volte la dimensione superiore a 2 secondi (2000 millisecondi). Il metodo [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) ottiene il valore corrente della proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) per l'inizio dell'animazione, quindi viene scalato da tale valore al valore più il primo argomento (2). In questo modo si garantisce che ogni animazione sarà sempre un ridimensionamento di 2 dalla posizione iniziale.

### <a name="scaling-and-rotation-with-anchors"></a>Ridimensionamento e rotazione con ancoraggi

Le proprietà [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) impostano il centro della scala o della rotazione per le proprietà [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) e [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) . Pertanto, i rispettivi valori influiscono anche sui metodi [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) e [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) .

Data una [`Image`](xref:Xamarin.Forms.Image) posizionata al centro di un layout, nell'esempio di codice seguente viene illustrata la rotazione dell'immagine intorno al centro del layout impostando la relativa proprietà [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) :

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

Per ruotare l'istanza di [`Image`](xref:Xamarin.Forms.Image) al centro del layout, le proprietà [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) devono essere impostate su valori relativi alla larghezza e all'altezza della `Image`. In questo esempio, il centro del `Image` viene definito al centro del layout, quindi il valore predefinito di `AnchorX` di 0,5 non richiede la modifica. Tuttavia, la proprietà `AnchorY` viene ridefinita come valore dall'inizio della `Image` al punto centrale del layout. In questo modo si garantisce che il `Image` esegue una rotazione completa di 360 gradi intorno al punto centrale del layout, come illustrato nelle schermate seguenti:

![](simple-images/rotate-anchors.png "Rotation Animation with Anchors")

### <a name="translation"></a>Conversione

Nell'esempio di codice riportato di seguito viene illustrato l'utilizzo del metodo [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) per aggiungere un'animazione alle proprietà [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) di un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Questo codice aggiunge un'animazione all'istanza di [`Image`](xref:Xamarin.Forms.Image) convertendo tale istanza orizzontalmente e verticalmente su 1 secondo (1000 millisecondi). Il [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo converte simultaneamente l'immagine 100 pixel a sinistra e 100 pixel verso l'alto. Questo è dovuto al fatto che il primo e il secondo argomento sono entrambi numeri negativi. Se si specificano numeri positivi, l'immagine verrà traslata a destra e in basso.

Gli screenshot seguenti mostrano la traduzione in corso in ogni piattaforma:

![](simple-images/translateto.png "Translation Animation")

> [!NOTE]
> Se un elemento viene inizialmente disposto allo schermo e quindi convertito sullo schermo, dopo la traduzione il layout di input dell'elemento rimane disattivato e l'utente non può interagire con esso. Pertanto, è consigliabile disporre di una vista nella posizione finale e quindi eseguire tutte le traduzioni richieste.

### <a name="fading"></a>Dissolvenza

Nell'esempio di codice riportato di seguito viene illustrato l'utilizzo del metodo [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) per aggiungere un'animazione alla proprietà [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) di un [`Image`](xref:Xamarin.Forms.Image):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Questo codice anima l'istanza di [`Image`](xref:Xamarin.Forms.Image) dissolvendo il contenuto in più di 4 secondi (4000 millisecondi). Il metodo [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) ottiene il valore corrente della proprietà [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) per l'inizio dell'animazione, quindi passa da tale valore al primo argomento (1).

Gli screenshot seguenti mostrano la dissolvenza in corso in ogni piattaforma:

![](simple-images/fadeto.png "Fading Animation")

<a name="compound" />

## <a name="compound-animations"></a>Animazioni composte

Un'animazione composta è una combinazione sequenziale di animazioni e può essere creata con l'operatore `await`, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

In questo esempio, il [`Image`](xref:Xamarin.Forms.Image) viene convertito oltre 6 secondi (6000 millisecondi). La traduzione del `Image` utilizza cinque animazioni, con l'operatore `await` che indica che ogni animazione viene eseguita in sequenza. Pertanto, i metodi di animazione successivi vengono eseguiti dopo il completamento del metodo precedente.

<a name="composite" />

## <a name="composite-animations"></a>Animazioni composite

Un'animazione composita è una combinazione di animazioni in cui due o più animazioni vengono eseguite simultaneamente. È possibile creare animazioni composite combinando animazioni attese e non attese, come illustrato nell'esempio di codice seguente:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

In questo esempio, il [`Image`](xref:Xamarin.Forms.Image) viene ridimensionato e ruotato simultaneamente in 4 secondi (4000 millisecondi). Il ridimensionamento del `Image` usa due animazioni sequenziali che si verificano contemporaneamente alla rotazione. Il metodo [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) viene eseguito senza un operatore `await` e viene restituito immediatamente, con la prima animazione [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) a partire da. L'operatore `await` sulla prima chiamata al metodo `ScaleTo` ritarda la seconda chiamata al metodo `ScaleTo` fino a quando non viene completata la prima chiamata al metodo `ScaleTo`. A questo punto l'animazione del `RotateTo` è completata a metà e il `Image` verrà ruotato di 180 gradi. Durante i 2 secondi finali (2000 millisecondi), il secondo `ScaleTo` animazione e l'animazione `RotateTo` sono completati.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Esecuzione simultanea di più metodi asincroni

I metodi `static` `Task.WhenAny` e `Task.WhenAll` vengono usati per eseguire contemporaneamente più metodi asincroni e possono quindi essere usati per creare animazioni composite. Entrambi i metodi restituiscono un oggetto `Task` e accettano una raccolta di metodi che restituiscono un oggetto `Task`. Il metodo `Task.WhenAny` viene completato al completamento dell'esecuzione di un metodo della raccolta, come illustrato nell'esempio di codice seguente:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

In questo esempio, la chiamata al metodo `Task.WhenAny` contiene due attività. La prima attività ruota l'immagine su 4 secondi (4000 millisecondi) e la seconda attività ridimensiona l'immagine in 2 secondi (2000 millisecondi). Al termine della seconda attività, viene completata la chiamata al metodo `Task.WhenAny`. Tuttavia, anche se il [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo è ancora in esecuzione, il secondo metodo [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) può iniziare.

Il metodo `Task.WhenAll` viene completato quando tutti i metodi nella relativa raccolta sono stati completati, come illustrato nell'esempio di codice seguente:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

In questo esempio, la chiamata al metodo `Task.WhenAll` contiene tre attività, ognuna delle quali viene eseguita in 10 minuti. Ogni `Task` rende un numero diverso di rotazioni di 360 gradi, ovvero 307 rotazioni per [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), 251 rotazioni per [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))e 199 rotazioni per [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)). Questi valori sono numeri primi, assicurando quindi che le rotazioni non siano sincronizzate e pertanto non comporteranno modelli ripetitivi.

Gli screenshot seguenti mostrano le rotazioni multiple in corso in ogni piattaforma:

![](simple-images/multiple-rotations.png "Composite Animation")

## <a name="canceling-animations"></a>Annullamento di animazioni

Un'applicazione può annullare una o più animazioni con una chiamata al metodo `static` [`ViewExtensions.CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) , come illustrato nell'esempio di codice seguente:

```csharp
ViewExtensions.CancelAnimations (image);
```

Verranno annullate immediatamente tutte le animazioni attualmente in esecuzione nell'istanza di [`Image`](xref:Xamarin.Forms.Image) .

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare e annullare animazioni usando la classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Questa classe fornisce metodi di estensione che possono essere usati per costruire animazioni semplici che ruotano, ridimensionano, traducono e sfumano [`VisualElement`](xref:Xamarin.Forms.VisualElement) istanze.

## <a name="related-links"></a>Collegamenti correlati

- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
- [Animazione di base (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
