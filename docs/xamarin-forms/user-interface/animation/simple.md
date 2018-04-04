---
title: Animazioni semplice
description: La classe ViewExtensions fornisce metodi di estensione che possono essere usati per costruire le animazioni semplice. In questo articolo illustra la creazione e l'annullamento delle animazioni utilizzando la classe ViewExtensions.
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: 0d2cc30f9bc1ae5602394b8ca2d8e75517a01b54
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="simple-animations"></a>Animazioni semplice

_La classe ViewExtensions fornisce metodi di estensione che possono essere usati per costruire le animazioni semplice. In questo articolo illustra la creazione e l'annullamento delle animazioni utilizzando la classe ViewExtensions._


Il [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe fornisce i seguenti metodi di estensione che possono essere usati per creare animazioni semplice:

- [`TranslateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) Aggiunge un'animazione di [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) e [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) le proprietà di un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`ScaleTo`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) Aggiunge un'animazione di [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) proprietà di un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RelScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) si applica un'animazione aumento incrementale o diminuirà nel [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) proprietà di un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RotateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) Aggiunge un'animazione di [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) proprietà di un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RelRotateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) si applica un'animazione aumento incrementale o diminuirà nel [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) proprietà di un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RotateXTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) Aggiunge un'animazione di [ `RotationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/) proprietà di un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RotateYTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) Aggiunge un'animazione di [ `RotationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/) proprietà di un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`FadeTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) Aggiunge un'animazione di [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) proprietà di un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).

Per impostazione predefinita, ogni animazione richiederà 250 millisecondi. Tuttavia, possibile specificare una durata per ogni animazione quando si crea l'animazione.

Il [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) include anche un [ `CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/) metodo che può essere utilizzato per annullare tutte le animazioni.

> [!NOTE]
> Il [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe fornisce un [ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/) metodo di estensione. Tuttavia, questo metodo deve essere utilizzato da layout per animare le transizioni tra stati layout contenenti dimensioni e posizione delle modifiche. Di conseguenza, e deve essere usato solo da [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) sottoclassi.

I metodi di estensione animazione di [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe sono tutti asincrona e restituito un `Task<bool>` oggetto. Il valore restituito è `false` se il completamento dell'animazione, e `true` se l'animazione è stata annullata. Di conseguenza, i metodi di animazione devono essere utilizzati in genere con la `await` operatore, che consente di determinare facilmente il completamento di un'animazione. Inoltre, l'esecuzione è possibile creare animazioni sequenziali con metodi di animazione successiva esecuzione dopo che il metodo precedente è stata completata. Per ulteriori informazioni, vedere [composta animazioni](#compound).

Se non c'è un requisito per consentire un'animazione completo in background, il `await` operatore può essere omesso. In questo scenario, i metodi di estensione animazione restituirà rapidamente dopo aver avviato l'animazione, con l'animazione che si verificano in background. Questa operazione può essere effettuata sfruttare durante la creazione di animazioni composite. Per ulteriori informazioni, vedere [animazioni composito](#composite).

Per ulteriori informazioni sul `await` (operatore), vedere [Cenni preliminari sul supporto di Async](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animazioni singole

Ogni metodo di estensione nel [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) implementa un'operazione singola animazione che progressivamente modifica una proprietà da un valore a un altro valore per un periodo di tempo. Questa sezione viene esaminata ogni operazione di animazione.

### <a name="rotation"></a>Rotazione

Esempio di codice seguente viene illustrato l'utilizzo di [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo per aggiungere un'animazione di [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) proprietà di un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Questo codice aggiunge un'animazione di [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza ruotando fino a 360 gradi maggiore di 2 secondi (2000 millisecondi). Il [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo ottiene l'oggetto corrente [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) proprietà valore per l'inizio dell'animazione e quindi Ruota da questo valore per il primo argomento (360). Una volta che l'animazione è completato, l'immagine [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) proprietà viene reimpostata su 0. In questo modo il `Rotation` proprietà non rimane in 360 dopo l'animazione è terminata, impedendo le rotazioni aggiuntive.

Le schermate seguenti mostrano la rotazione in corso in ogni piattaforma:

![](simple-images/rotateto.png "Animazione di rotazione")

### <a name="relative-rotation"></a>Rotazione relativa

Esempio di codice seguente viene illustrato l'utilizzo di [ `RelRotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo per aumentare o diminuire in modo incrementale la [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) proprietà di un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RelRotateTo (360, 2000);
```

Questo codice aggiunge un'animazione di [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza dalla rotazione di 360 gradi dalla posizione iniziale maggiore di 2 secondi (2000 millisecondi). Il [ `RelRotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo ottiene l'oggetto corrente [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) proprietà valore per l'inizio dell'animazione e quindi Ruota da questo valore per il valore e per il primo argomento (360). In questo modo si garantisce che ogni animazione sarà sempre una rotazione di 360 gradi dalla posizione iniziale. Pertanto, se una nuova animazione viene richiamata durante l'esecuzione di un'animazione, verrà avviato dalla posizione corrente e potrebbe terminare in una posizione che non è un incremento di 360 gradi.

Le schermate seguenti mostrano la rotazione relativa in corso in ogni piattaforma:

![](simple-images/relrotateto.png "Animazione di rotazione relativo")

### <a name="scaling"></a>Ridimensionamento

Esempio di codice seguente viene illustrato l'utilizzo di [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) metodo per aggiungere un'animazione di [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) proprietà di un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.ScaleTo (2, 2000);
```

Questo codice aggiunge un'animazione di [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza mediante la scalabilità verticale per due volte la dimensione maggiore di 2 secondi (2000 millisecondi). Il [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) metodo ottiene l'oggetto corrente [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) valore della proprietà (valore predefinito di 1) per l'inizio dell'animazione e quindi Scale da questo valore per il primo argomento (2). Questo ha l'effetto di espandere le dimensioni dell'immagine per due volte la dimensione.

Le schermate seguenti mostrano il ridimensionamento in corso in ogni piattaforma:

![](simple-images/scaleto.png "Ridimensionamento di animazione")

### <a name="relative-scaling"></a>Ridimensionamento relative

Esempio di codice seguente viene illustrato l'utilizzo di [ `RelScaleTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo per aggiungere un'animazione di [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) proprietà di un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RelScaleTo (2, 2000);
```

Questo codice aggiunge un'animazione di [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza mediante la scalabilità verticale per due volte la dimensione maggiore di 2 secondi (2000 millisecondi). Il [ `RelScaleTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo ottiene l'oggetto corrente [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) valore della proprietà per l'inizio dell'animazione e quindi Scale da questo valore per il valore e per il primo argomento (2). In questo modo si garantisce che ogni animazione sarà sempre una scala di 2 dalla posizione iniziale.

### <a name="scaling-and-rotation-with-anchors"></a>Scalabilità e la rotazione con punti di ancoraggio

Il [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) e [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) le proprietà impostate al centro della scala o la rotazione per il [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) e [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) proprietà. Di conseguenza, i relativi valori influenzeranno la [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) e [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) metodi.

Dato un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) che è stato inserito al centro di un layout, l'esempio di codice seguente illustra la rotazione l'immagine intorno al centro del layout impostando il relativo [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) proprietà:

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

Per ruotare il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza attorno al centro del layout, il [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) e [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) devono essere impostate su valori rispetto alla larghezza e altezza del `Image`. In questo esempio, al centro del `Image` definite per il centro del layout e pertanto il valore predefinito `AnchorX` valore pari a 0,5 non implica la modifica. Tuttavia, il `AnchorY` proprietà ridefinita come un valore dalla cima il `Image` al punto centrale del layout. In questo modo il `Image` effettua una rotazione completa di 360 gradi attorno al punto centrale del layout, come illustrato nelle schermate seguenti:

![](simple-images/rotate-anchors.png "Animazione di rotazione con punti di ancoraggio")

### <a name="translation"></a>Conversione

Esempio di codice seguente viene illustrato l'utilizzo di [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo per aggiungere un'animazione di [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) e [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) le proprietà di un [ `Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Questo codice aggiunge un'animazione di [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza traducendo orizzontalmente e verticalmente in 1 secondo (1000 millisecondi). Il [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo converte contemporaneamente i pixel dell'immagine 100 a sinistra e verso l'alto di 100 pixel. Questo avviene perché il primo e secondo argomento è entrambi i numeri negativi. Fornire i numeri positivi, è necessario convertire l'immagine a destra e verso il basso.

La schermata seguente mostra la conversione in corso in ogni piattaforma:

![](simple-images/translateto.png "Animazione di traduzione")

> [!NOTE]
> Se un elemento è inizialmente disposti fuori dello schermo e quindi convertito sullo schermo, dopo la conversione di input il layout dell'elemento rimane fuori dello schermo e l'utente non può interagire con esso. Pertanto, è consigliabile che una vista deve essere disposto in una posizione finale, e quindi le necessarie conversioni eseguite.

### <a name="fading"></a>Dissolvenza

Esempio di codice seguente viene illustrato l'utilizzo di [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo per aggiungere un'animazione di [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) proprietà di un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Questo codice aggiunge un'animazione di [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza con oltre 4 secondi (4000 millisecondi). Il [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo ottiene l'oggetto corrente [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) valore della proprietà per l'inizio dell'animazione e quindi dissolvenza in da questo valore per il primo argomento (1).

Le schermate seguenti mostrano la dissolvenza in corso in ogni piattaforma:

![](simple-images/fadeto.png "Animazione di dissolvenza")

<a name="compound" />

## <a name="compound-animations"></a>Animazioni composte

Un'animazione composta è una combinazione di animazioni sequenziale e possono essere creata con il `await` (operatore), come illustrato nell'esempio di codice seguente:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

In questo esempio, il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) viene convertito in 6 secondi (6000 millisecondi). La conversione del `Image` Usa animazioni di cinque, con la `await` operatore che indica che ogni animazione esegue in sequenza. Di conseguenza, i metodi di animazione successivo eseguito dopo il metodo precedente è stata completata.

<a name="composite" />

## <a name="composite-animations"></a>Animazioni composite

Un'animazione composita è una combinazione di animazioni in cui due o più animazioni eseguire simultaneamente. È possibile creare animazioni composite combinando le animazioni di attese e non atteso, come illustrato nell'esempio di codice seguente:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

In questo esempio, il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) scala e contemporaneamente ruotato (4000 millisecondi) oltre 4 secondi. La scalabilità del `Image` utilizza due animazioni sequenziali che si verificano durante la rotazione. Il [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo viene eseguito senza un `await` operatore e viene restituito immediatamente, con il primo [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) animazione quindi a partire da. Il `await` operatore sul primo `ScaleTo` chiamata al metodo Ritarda la seconda `ScaleTo` fino a quando la prima chiamata al metodo `ScaleTo` chiamata al metodo è stata completata. A questo punto il `RotateTo` animazione è metà modo completato e `Image` verrà ruotata di 180 gradi. Durante il finali 2 secondi (2000 millisecondi), il secondo `ScaleTo` animazione e `RotateTo` animazione sia completata.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Esecuzione contemporanea di più metodi asincroni

Il `static` `Task.WhenAny` e `Task.WhenAll` metodi utilizzati per eseguire contemporaneamente più metodi asincroni e pertanto può essere utilizzati per creare animazioni composite. Entrambi i metodi restituiscono un `Task` dell'oggetto e di accettare una raccolta di metodi che ogni restituito un `Task` oggetto. Il `Task.WhenAny` metodo viene completato al completamento di qualsiasi metodo nella propria raccolta di esecuzione, come illustrato nell'esempio di codice seguente:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

In questo esempio, il `Task.WhenAny` chiamata al metodo contiene due attività. La prima attività Ruota l'immagine di oltre 4 secondi (in 4000 millisecondi) e la seconda attività Ridimensiona l'immagine di maggiore di 2 secondi (2000 millisecondi). Al termine, la seconda attività di `Task.WhenAny` completamento della chiamata di metodo. Tuttavia, anche se il [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) metodo è ancora in esecuzione, il secondo [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) metodo può iniziare.

Il `Task.WhenAll` metodo viene completato quando completati tutti i metodi di insieme, come illustrato nell'esempio di codice seguente:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

In questo esempio, il `Task.WhenAll` chiamata al metodo contiene tre attività, ognuna delle quali viene eseguito più di 10 minuti. Ogni `Task` rende un numero diverso di 360 gradi rotazioni – 307 rotazioni per [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), 251 rotazioni per [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)e 199 rotazioni per [ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/). Questi valori sono i numeri primi, assicurando pertanto che la rotazione non sono sincronizzata e pertanto non comporta la modelli ricorrenti.

Le schermate seguenti mostrano le rotazioni più in corso in ogni piattaforma:

![](simple-images/multiple-rotations.png "Animazione composito")

## <a name="canceling-animations"></a>Annullamento di animazioni

Un'applicazione può annullare una o più animazioni con una chiamata al `static` [ `ViewExtensions.CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/) (metodo), come illustrato nell'esempio di codice seguente:

```csharp
ViewExtensions.CancelAnimations (image);
```

Verrà immediatamente annullata tutte le animazioni che attualmente eseguono il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) istanza.

## <a name="summary"></a>Riepilogo

Questo articolo sono state illustrate la creazione e l'annullamento di animazioni mediante il [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe. Questa classe fornisce metodi di estensione che possono essere usati per costruire le animazioni semplice ruotare, ridimensionare, convertono e dissolvenza [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) istanze.


## <a name="related-links"></a>Collegamenti correlati

- [Panoramica del supporto asincrono](~/cross-platform/platform/async.md)
- [Animazione di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/basic/)
- [ViewExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)
