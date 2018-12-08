---
title: Animazioni semplici in xamarin. Forms
description: La classe ViewExtensions fornisce metodi di estensione che possono essere utilizzati per costruire semplici animazioni. Questo articolo illustra la creazione e l'annullamento di animazioni tramite la classe ViewExtensions.
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: 93bb138b3a78b6052715ab987a4634a18cdce317
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53054502"
---
# <a name="simple-animations-in-xamarinforms"></a>Animazioni semplici in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/basic/)

_La classe ViewExtensions fornisce metodi di estensione che possono essere utilizzati per costruire semplici animazioni. Questo articolo illustra la creazione e l'annullamento di animazioni tramite la classe ViewExtensions._


Il [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe fornisce i seguenti metodi di estensione che possono essere utilizzati per creare animazioni semplici:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) Aggiunge un'animazione la [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) le proprietà di un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`ScaleTo`](xref:Xamarin.Forms.VisualElement.Scale) Aggiunge un'animazione la [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) si applica un aumento incrementale animata o diminuirà in base il [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Aggiunge un'animazione la [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) proprietà di un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) si applica un aumento incrementale animata o diminuirà in base il [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) proprietà di un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Aggiunge un'animazione la [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX) proprietà di un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Aggiunge un'animazione la [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY) proprietà di un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Aggiunge un'animazione la [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) proprietà di un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).

Per impostazione predefinita, ogni animazione richiederà 250 millisecondi. Tuttavia, possibile specificare una durata di ogni animazione quando si crea l'animazione.

Il [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe include anche una [ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) metodo che può essere utilizzato per annullare tutte le animazioni.

> [!NOTE]
> Il [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe fornisce una [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)) metodo di estensione. Tuttavia, questo metodo è destinato a utilizzate da layout per animare le transizioni tra stati layout contenenti dimensioni e posizione delle modifiche. Pertanto, deve essere utilizzato solo da [ `Layout` ](xref:Xamarin.Forms.Layout) sottoclassi.

Metodi di estensione animazione la [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe sono tutti asincroni e restituito un `Task<bool>` oggetto. Il valore restituito sarà `false` se il completamento dell'animazione, e `true` se l'animazione è stata annullata. Di conseguenza, i metodi di animazione devono essere utilizzati in genere con la `await` operatore, che consente di determinare facilmente se è stata completata un'animazione. Inoltre, l'esecuzione è possibile creare animazioni sequenziali con metodi di animazione successivo per l'esecuzione dopo il metodo precedente è stata completata. Per altre informazioni, vedere [composta animazioni](#compound).

Se è presente un requisito per consentire a un'animazione completo in background, il `await` operatore può essere omesso. In questo scenario, i metodi di estensione animazione restituirà rapidamente dopo l'avvio dell'animazione, con l'animazione si verifica in background. Questa operazione può avvantaggiarsi della durante la creazione di animazioni composite. Per altre informazioni, vedere [animazioni composito](#composite).

Per altre informazioni sul `await` operatore, vedere [Cenni preliminari sul supporto di Async](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animazioni singole

Ogni metodo di estensione nel [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) implementa un'operazione singola animazione progressivamente modifica una proprietà da un valore a un altro valore in un periodo di tempo. Questa sezione viene esaminata ogni operazione di animazione.

### <a name="rotation"></a>Rotazione

Esempio di codice seguente viene illustrato come utilizzare il [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo per aggiungere un'animazione la [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) proprietà di un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Questo codice aggiunge un'animazione la [ `Image` ](xref:Xamarin.Forms.Image) istanza dalla rotazione fino a 360 gradi maggiore di 2 secondi (2000 millisecondi). Il [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo ottiene l'oggetto corrente [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) proprietà value per l'inizio dell'animazione e quindi Ruota da tale valore per il primo argomento (360). Una volta che l'animazione è completato, l'immagine [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) proprietà viene reimpostata su 0. Ciò garantisce che il `Rotation` proprietà non rimane in 360 al termine dell'animazione, che possono impedire le rotazioni aggiuntive.

Le schermate seguenti illustrano la rotazione in corso in ogni piattaforma:

![](simple-images/rotateto.png "Animazione di rotazione")

### <a name="relative-rotation"></a>Rotazione relativa

Esempio di codice seguente viene illustrato come utilizzare il [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo per aumentare o ridurre in modo incrementale la [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) proprietà di un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelRotateTo (360, 2000);
```

Questo codice aggiunge un'animazione la [ `Image` ](xref:Xamarin.Forms.Image) istanza dalla rotazione 360 gradi dalla posizione iniziale di maggiore di 2 secondi (2000 millisecondi). Il [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo ottiene l'oggetto corrente [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) proprietà value per l'inizio dell'animazione e quindi Ruota da tale valore per il valore più il primo argomento (360). Ciò garantisce che ogni animazione sarà sempre una rotazione di 360 gradi dalla posizione iniziale. Pertanto, se una nuova animazione viene richiamata durante l'esecuzione di un'animazione in stato di avanzamento, inizierà dalla posizione corrente e può terminare in una posizione che non è un incremento di 360 gradi.

Le schermate seguenti illustrano la rotazione relativa in corso in ogni piattaforma:

![](simple-images/relrotateto.png "Animazione di rotazione relativo")

### <a name="scaling"></a>Ridimensionamento

Esempio di codice seguente viene illustrato come utilizzare il [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) metodo per aggiungere un'animazione la [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.ScaleTo (2, 2000);
```

Questo codice aggiunge un'animazione la [ `Image` ](xref:Xamarin.Forms.Image) istanza mediante la scalabilità verticale a due volte la dimensione maggiore di 2 secondi (2000 millisecondi). Il [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) metodo ottiene l'oggetto corrente [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) valore della proprietà (valore predefinito pari a 1) per l'inizio dell'animazione e quindi ridimensiona da tale valore per il primo argomento (2). Questo ha l'effetto di espandere le dimensioni dell'immagine per due volte la dimensione.

Gli screenshot seguenti mostrano il ridimensionamento in corso in ogni piattaforma:

![](simple-images/scaleto.png "Ridimensionamento di animazione")

> [!NOTE]
> Il [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe definisce anche [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) e [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) proprietà, che è possibile ridimensionare il `VisualElement` in modo diverso nel direzioni orizzontale e verticale. Queste proprietà possono essere animate con il [ `Animation` ](xref:Xamarin.Forms.Animation) classe. Per altre informazioni, vedere [animazioni personalizzate in xamarin. Forms](custom.md).

### <a name="relative-scaling"></a>Ridimensionamento relative

Esempio di codice seguente viene illustrato come utilizzare il [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo per aggiungere un'animazione la [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) proprietà di un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelScaleTo (2, 2000);
```

Questo codice aggiunge un'animazione la [ `Image` ](xref:Xamarin.Forms.Image) istanza mediante la scalabilità verticale a due volte la dimensione maggiore di 2 secondi (2000 millisecondi). Il [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo ottiene l'oggetto corrente [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) valore della proprietà per l'inizio dell'animazione e quindi Scale da tale valore per il valore più il primo argomento (2). Ciò garantisce che ogni animazione sarà sempre un ridimensionamento pari a 2 dalla posizione iniziale.

### <a name="scaling-and-rotation-with-anchors"></a>La scalabilità e la rotazione con punti di ancoraggio

Il [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) e [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) impostate al centro del ridimensionamento o rotazione per il [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) e [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) proprietà. Di conseguenza, i relativi valori influenzeranno anche il [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) e [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) metodi.

Dato un [ `Image` ](xref:Xamarin.Forms.Image) che è stato inserito il fulcro di un layout, il codice seguente illustra la rotazione intorno al centro del layout dell'immagine tramite l'impostazione relativa [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) proprietà:

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

Per ruotare il [ `Image` ](xref:Xamarin.Forms.Image) istanza intorno al centro del layout, il [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) e [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) devono essere impostate su valori rispetto alla larghezza e altezza del `Image`. In questo esempio, il centro del `Image` viene definito come il fulcro del layout e pertanto il valore predefinito `AnchorX` valore pari a 0,5 non richiede la modifica. Tuttavia, il `AnchorY` proprietà viene ridefinito in modo da essere un valore dalla cima il `Image` al punto centrale del layout. Ciò garantisce che il `Image` effettua una rotazione completa di 360 gradi intorno al punto centrale del layout, come illustrato negli screenshot seguenti:

![](simple-images/rotate-anchors.png "Animazione di rotazione con punti di ancoraggio")

### <a name="translation"></a>Conversione

Esempio di codice seguente viene illustrato come utilizzare il [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo per aggiungere un'animazione la [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) le proprietà di un [ `Image`](xref:Xamarin.Forms.Image):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Questo codice aggiunge un'animazione la [ `Image` ](xref:Xamarin.Forms.Image) istanza traslandolo orizzontalmente e verticalmente più di 1 secondo (1000 millisecondi). Il [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo contemporaneamente si traduce i pixel delle immagini 100 a sinistra e verso l'alto di 100 pixel. Questo avviene perché il primo e secondo argomento è entrambi i numeri negativi. Fornire i numeri positivi convertiranno l'immagine a destra e verso il basso.

Le schermate seguenti illustrano la traduzione in corso in ogni piattaforma:

![](simple-images/translateto.png "Animazione di traduzione")

> [!NOTE]
> Se un elemento è inizialmente disposti fuori dallo schermo e quindi convertite sullo schermo, dopo la conversione di input il layout dell'elemento rimane fuori dallo schermo e l'utente non è possibile interagire con esso. Pertanto, è consigliabile che una vista deve essere disposto nella relativa posizione finale, e quindi le necessarie conversioni eseguite.

### <a name="fading"></a>Dissolvenza

Esempio di codice seguente viene illustrato come utilizzare il [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo per aggiungere un'animazione la [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) proprietà di un [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Questo codice aggiunge un'animazione la [ `Image` ](xref:Xamarin.Forms.Image) istanza con in più di 4 secondi (in 4000 millisecondi). Il [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo ottiene l'oggetto corrente [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) valore della proprietà per l'inizio dell'animazione e quindi dissolvenze in da tale valore per il primo argomento (1).

Gli screenshot seguenti mostrano l'effetto di dissolvenza in corso in ogni piattaforma:

![](simple-images/fadeto.png "Animazione di dissolvenza della")

<a name="compound" />

## <a name="compound-animations"></a>Animazioni composte

Un'animazione composta è una combinazione di animazioni sequenza e può essere creata con il `await` operatore, come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

In questo esempio, il [ `Image` ](xref:Xamarin.Forms.Image) viene convertito in 6 secondi (in 6000 millisecondi). La conversione del `Image` Usa cinque animazioni, con la `await` operatore che indica che ogni animazione viene eseguita in modo sequenziale. Di conseguenza, i metodi di animazione successivo eseguito dopo che il metodo precedente è stata completata.

<a name="composite" />

## <a name="composite-animations"></a>Animazioni composite

Un'animazione composita è una combinazione di animazioni in cui due o più animazioni eseguito contemporaneamente. È possibile creare animazioni composite combinando le animazioni di attese e non atteso, come illustrato nell'esempio di codice seguente:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

In questo esempio, il [ `Image` ](xref:Xamarin.Forms.Image) scala e ruotato contemporaneamente più di 4 secondi (in 4000 millisecondi). Il ridimensionamento del `Image` usa due animazioni sequenziale che si verificano in concomitanza con la rotazione. Il [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo viene eseguito senza un' `await` operatore e restituisce immediatamente, con il primo [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) animazione quindi iniziare. Il `await` operatore sul primo `ScaleTo` chiamata al metodo Ritarda la seconda `ScaleTo` chiamata al metodo fino al primo `ScaleTo` chiamata al metodo è stata completata. A questo punto il `RotateTo` animazione è metà modo in cui è stata completata e il `Image` sarà ruotati di 180 gradi. Durante il finali 2 secondi (2000 millisecondi), il secondo `ScaleTo` animazione e `RotateTo` animazione sia completata.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Esecuzione simultanea di più metodi asincroni

Il `static` `Task.WhenAny` e `Task.WhenAll` metodi utilizzati per eseguire contemporaneamente più metodi asincroni e pertanto può essere utilizzati per creare animazioni composite. Entrambi i metodi restituiscono un `Task` dell'oggetto e accettare una raccolta di metodi che ogni restituito un `Task` oggetto. Il `Task.WhenAny` metodo viene completato al completamento di qualsiasi metodo nella propria raccolta di esecuzione, come illustrato nell'esempio di codice seguente:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

In questo esempio, il `Task.WhenAny` chiamata al metodo contiene due attività. La prima attività consente di ruotare l'immagine più di 4 secondi (4000 in millisecondi) e la seconda attività Ridimensiona l'immagine di maggiore di 2 secondi (2000 millisecondi). Al termine, la seconda attività di `Task.WhenAny` completamento della chiamata di metodo. Tuttavia, anche se il [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) metodo è ancora in esecuzione, il secondo [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) metodo può iniziare.

Il `Task.WhenAll` metodo viene completato quando tutti i metodi nel relativo insieme sono completate, come illustrato nell'esempio di codice seguente:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

In questo esempio, il `Task.WhenAll` chiamata al metodo contiene tre attività, ognuna delle quali viene eseguito più di 10 minuti. Ciascuna `Task` effettua un numero diverso di rotazioni a 360 gradi – 307 rotazioni per [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), 251 rotazioni per [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))e di 199 rotazioni per [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)). Questi valori sono i numeri primi, pertanto verificare che le rotazioni non sono sincronizzate e pertanto non comporta la modelli ricorrenti.

Le schermate seguenti illustrano le rotazioni più in corso in ogni piattaforma:

![](simple-images/multiple-rotations.png "Animazione composito")

## <a name="canceling-animations"></a>Annullamento di animazioni

Un'applicazione può annullare una o più animazioni con una chiamata per il `static` [ `ViewExtensions.CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) metodo, come illustrato nell'esempio di codice seguente:

```csharp
ViewExtensions.CancelAnimations (image);
```

Verrà immediatamente annullata tutte le animazioni che attualmente eseguono il [ `Image` ](xref:Xamarin.Forms.Image) istanza.

## <a name="summary"></a>Riepilogo

Questo articolo sono state illustrate la creazione e l'annullamento di animazioni tramite le [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe. Questa classe fornisce metodi di estensione che possono essere utilizzati per costruire semplici animazioni per ruotano, ridimensionano, traducono e dissolvenza [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) istanze.


## <a name="related-links"></a>Collegamenti correlati

- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
- [Animazione di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/basic/)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
