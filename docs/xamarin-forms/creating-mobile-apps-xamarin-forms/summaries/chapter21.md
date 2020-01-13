---
title: Riepilogo del capitolo 21. Trasformazioni
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: Riepilogo del capitolo 21. Trasformazioni'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760573"
---
# <a name="summary-of-chapter-21-transforms"></a>Riepilogo del capitolo 21. Trasformazioni

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Una vista di Xamarin.Forms viene visualizzato sullo schermo in una posizione e dimensione determinata dal relativo elemento padre, che corrisponde in genere un `Layout` o `Layout<View>` derivato. Il *trasformare* è una funzionalità di Xamarin.Forms che è possibile modificare tale posizione, dimensione o orientamento anche.

Xamarin.Forms supporta tre tipi di trasformazioni di base:

- *Traduzione* &mdash; spostare un elemento orizzontalmente o verticalmente
- *Scala* &mdash; modificare le dimensioni di un elemento
- *Rotazione* &mdash; attiva un elemento intorno a un punto o un asse

In Xamarin.Forms, il ridimensionamento è isotropo; influisce su larghezza e altezza in modo uniforme. La rotazione è supportata sia nella superficie bidimensionale dello schermo e nello spazio 3D. Non vi è alcuna trasformazione di inclinazione (o elevata) e Nessuna trasformazione di matrice generalizzato.

Le trasformazioni sono supportate con otto proprietà typu `double` definito dal `VisualElement` classe:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Tutte queste proprietà sono supportate da proprietà associabili. Possono essere destinazioni di data binding e stile. [**Capitolo 22. Animazione** ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) di seguito viene illustrato come queste proprietà possono essere animate, ma alcuni negli esempi in questo capitolo viene illustrato come è possibile aggiungere un'animazione usando Xamarin.Forms [timer](~/xamarin-forms/platform/device.md#devicestarttimer).

Proprietà hanno effetto solo come l'elemento viene eseguito il rendering e si trasformano *non* influiscono sul modo in cui l'elemento viene percepito nel layout.

## <a name="the-translation-transform"></a>La trasformazione di traslazione

Valori diversi da zero del [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) proprietà spostare un elemento orizzontalmente o verticalmente.

Il [ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) programma consente di provare a usare queste proprietà con due `Slider` elementi che controllano la `TranslationX` e `TranslationY` le proprietà di un `Frame`. La trasformazione influisce inoltre su tutti gli elementi figlio di tale `Frame`.

### <a name="text-effects"></a>Effetti di testo

Viene in genere utilizzato le proprietà di traslazione di offset leggermente il rendering del testo. Questa funzionalità viene illustrata la [ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) esempio:

[![Schermata triplo del testo viene eseguito l'offset](images/ch21fg03-small.png "testo Offsets")](images/ch21fg03-large.png#lightbox "testo viene eseguito l'offset")

Un altro effetto sia per il rendering di più copie di un `Label` simile a quella di un blocco 3D, come illustrato nel [ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) esempio.

### <a name="jumps-and-animations"></a>Improvvisi e animazioni

Il [ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) esempio Usa la traduzione per spostare un `Button` ogni volta che viene toccato, ma lo scopo primario è di dimostrare che il `Button` riceve l'input utente in corrispondenza della posizione in cui il pulsante viene eseguito il rendering.

Il [ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) esempio è simile, ma usa un timer per aggiungere un'animazione di `Button` da un punto a altro.

## <a name="the-scale-transform"></a>Trasformazione di ridimensionamento

Il [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) trasformazione è possibile aumentare o ridurre le dimensioni di rendering dell'elemento. Il valore predefinito è 1. Un valore pari a 0 fa sì che l'elemento sia invisibile. I valori negativi che l'elemento venga visualizzato deve essere ruotato 180 gradi. Il `Scale` proprietà influisce il `Width` o `Height` proprietà dell'elemento. Tali valori sono uguali.

È possibile sperimentare il `Scale` proprietà usando la [ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) esempio.

Il [ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) esempio illustra la differenza tra l'animazione la `Scale` proprietà di un `Button` e animazione di `FontSize` proprietà. Il `FontSize` proprietà ha effetto sul modo in cui il `Button` viene percepito nel layout; il `Scale` proprietà non.

Il [ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) esempio calcola una `Scale` proprietà a cui viene applicato un `Label` elemento per renderlo più grande possibile mentre si adatti all'interno della pagina.

### <a name="anchoring-the-scale"></a>Ancoraggio della scala

Gli elementi ridimensionati nei tre esempi precedenti hanno tutti aumenta o diminuisce le dimensioni rispetto al centro dell'elemento. In altre parole, l'elemento aumenta o diminuisce le dimensioni lo stesso in tutte le direzioni. Durante il ridimensionamento, solo il punto al centro dell'elemento rimane nella stessa posizione.

È possibile modificare il centro della scala, impostando il [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) e [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) proprietà. Queste proprietà sono rispetto all'elemento stesso. Per `AnchorX`, lato sinistro dell'elemento si intende un valore pari a 0 e 1 si riferisce al lato destro. Analogamente, per `AnchorY`, 0 è la parte superiore e inferiore è 1. Entrambe le proprietà hanno valori predefiniti pari a 0,5, che corrisponde al punto centrale.

Il [ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) esempio consente di sperimentare il `AnchorX` e `AnchorY` proprietà così come il `Scale` proprietà.

In iOS, utilizzando i valori non predefiniti dei `AnchorX` e `AnchorY` proprietà non è in genere compatibile con le modifiche apportate orientamento phone.

## <a name="the-rotation-transform"></a>La trasformazione di rotazione

Il [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) proprietà viene specificata in gradi e indica una rotazione in senso orario intorno a un punto dell'elemento definito da `AnchorX` e `AnchorY`. Il [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) consente di sperimentare queste tre proprietà.

### <a name="rotated-text-effects"></a>Effetti di testo ruotato

Il [ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) esempio illustra la matematica necessaria disegnare un cerchio con 64 piccolo ruotato `BoxView` elementi.

Il [ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) esempio vengono visualizzati più `Label` ruotare gli elementi con la stessa stringa di testo viene visualizzato come spoke.

Il [ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) esempio visualizza una stringa di testo che viene eseguito il wrapping in un cerchio.

### <a name="an-analog-clock"></a>Un orologio analogico

Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) library contiene un' [ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) classe che calcola gli angoli per messi a disposizione di un orologio. Per evitare le dipendenze della piattaforma nel ViewModel, la classe Usa `Task.Delay` anziché un timer per la ricerca di un nuovo `DateTime` valore.

Include anche **Xamarin.FormsBook.Toolkit** è un [ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) classe che implementa `IValueConverter` e viene usato per arrotondare un angolo secondo al secondo più vicino.

Il [ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) Usa tre rotazione `BoxView` elementi per disegnare un orologio analogico.

Il [ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) Usa `BoxView` per gli elementi grafici più esteso, inclusi segni di graduazione il quadrante dell'orologio tra virgolette e consegna che ruota una distanza poco da loro end:

[![Tripla screenshot dell'orologio BoxView](images/ch21fg17-small.png "quadrante dell'orologio analogico")](images/ch21fg17-large.png#lightbox "quadrante dell'orologio analogico")

Inoltre un' [ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) classe **Xamarin.FormsBook.Toolkit** fa sì che l'icona della mano secondo venga visualizzato per il pull back un po' prima di cimentarci e quindi spostare nuovamente nella posizione corretta.

### <a name="vertical-sliders"></a>Dispositivi di scorrimento verticale?

Il [ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) esempio dimostra che `Slider` elementi possono essere ruotati di 90 gradi e continuano a funzionare. Tuttavia, è difficile posizionare questi ruotato `Slider` elementi poiché nel layout vengono comunque visualizzati su orizzontale.

## <a name="3d-ish-rotations"></a>Rotazioni 3D ICA

Il [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX) proprietà viene visualizzata per ruotare un elemento intorno a un asse x 3D in modo che sembrano sposta verso o allontanarsi superiore e inferiore dell'elemento. Analogamente, il [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY) sembra ruotare un elemento intorno all'asse y per rendere i lati sinistro e destro dell'elemento sembra sposta verso o allontanarsi.

Il `AnchorX` proprietà influisce `RotationY` ma non `RotationX`. Il `AnchorY` proprietà influisce `RotationX` ma non `RotationY`. È possibile sperimentare il [ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) esempio per esplorare le interazioni di queste proprietà.

Il sistema di coordinate 3D in cui è inclusa per Xamarin.Forms è da sinistra. Se si punta l'indice della propria mano a sinistra nella direzione di incremento X coordina (a destra) e coordina il dito intermedio nella direzione y crescente (inattivo), quindi i punti di controllo thumb nella direzione crescente le coordinate Z (su schermo).

Inoltre, per uno dei tre assi, se si posiziona il cursore a sinistra nella direzione di aumentare i valori, quindi la curva delle dita indica la direzione di rotazione per gli angoli di rotazione positivi.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 21 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Capitolo 21 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
