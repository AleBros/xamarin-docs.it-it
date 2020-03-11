---
title: Riepilogo del capitolo 21. Trasformazioni
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 21. Trasformazioni'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760573"
---
# <a name="summary-of-chapter-21-transforms"></a>Riepilogo del capitolo 21. Trasformazioni

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Una visualizzazione Novell. Forms viene visualizzata sullo schermo in una posizione e in una dimensione determinate dal relativo elemento padre, che in genere è un `Layout` o `Layout<View>` derivato. La *trasformazione* è una funzionalità di Novell. Forms che può modificare la posizione, le dimensioni o persino l'orientamento.

Xamarin. Forms supporta tre tipi di trasformazioni di base:

- *Traslazione* &mdash; spostare un elemento orizzontalmente o verticalmente
- *Ridimensionare* &mdash; modificare le dimensioni di un elemento
- *Rotazione* &mdash; trasformare un elemento intorno a un punto o un asse

In xamarin. Forms, il ridimensionamento è isotropo; influisce su larghezza e altezza in modo uniforme. La rotazione è supportata sia nella superficie bidimensionale dello schermo e nello spazio 3D. Non vi è alcuna trasformazione di inclinazione (o elevata) e Nessuna trasformazione di matrice generalizzato.

Le trasformazioni sono supportate con otto proprietà di tipo `double` definite dalla classe `VisualElement`:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Tutte queste proprietà sono supportate da proprietà associabili. Possono essere destinazioni di data binding e stile. [**Capitolo 22. L'animazione**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) illustra il modo in cui queste proprietà possono essere animate, ma alcuni esempi in questo capitolo illustrano come è possibile animarli usando il [timer](~/xamarin-forms/platform/device.md#devicestarttimer)Novell. Forms.

Le proprietà di trasformazione influiscono solo sulla modalità di rendering dell'elemento e *non* influiscono sul modo in cui l'elemento viene percepito nel layout.

## <a name="the-translation-transform"></a>La trasformazione di traslazione

Valori diversi da zero delle proprietà [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) spostare un elemento orizzontalmente o verticalmente.

Il programma [**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) consente di sperimentare queste proprietà con due elementi `Slider` che controllano le proprietà `TranslationX` e `TranslationY` di una `Frame`. La trasformazione influiscono anche su tutti gli elementi figlio di tale `Frame`.

### <a name="text-effects"></a>Effetti di testo

Viene in genere utilizzato le proprietà di traslazione di offset leggermente il rendering del testo. Questa operazione è illustrata nell'esempio [**TextOffsets**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) :

[![Schermata tripla degli offset di testo](images/ch21fg03-small.png "Offset di testo")](images/ch21fg03-large.png#lightbox "Offset di testo")

Un altro effetto consiste nel eseguire il rendering di più copie di un `Label` in modo analogo a un blocco 3D, come illustrato nell'esempio [**BlockText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) .

### <a name="jumps-and-animations"></a>Improvvisi e animazioni

L'esempio [**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) usa la traduzione per spostare un `Button` ogni volta che viene toccato, ma lo scopo principale consiste nel dimostrare che la `Button` riceve l'input dell'utente nella posizione in cui viene eseguito il rendering del pulsante.

L'esempio [**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) è simile, ma usa un timer per animare il `Button` da un punto a un altro.

## <a name="the-scale-transform"></a>Trasformazione di ridimensionamento

La trasformazione [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) può aumentare o diminuire la dimensione di cui è stato eseguito il rendering dell'elemento. Il valore predefinito è 1. Un valore pari a 0 fa sì che l'elemento sia invisibile. I valori negativi che l'elemento venga visualizzato deve essere ruotato 180 gradi. La proprietà `Scale` non influisce sulle proprietà `Width` o `Height` dell'elemento. Tali valori sono uguali.

È possibile sperimentare la proprietà `Scale` usando l'esempio [**SimpleScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) .

Nell'esempio [**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) viene illustrata la differenza tra l'animazione della proprietà `Scale` di un `Button` e l'animazione della proprietà `FontSize`. La proprietà `FontSize` influiscono sul modo in cui il `Button` viene percepito nel layout; il `Scale` proprietà non è.

Nell'esempio [**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) viene calcolata una proprietà `Scale` applicata a un elemento `Label` per renderla la più grande possibile mentre è ancora adatta all'interno della pagina.

### <a name="anchoring-the-scale"></a>Ancoraggio della scala

Gli elementi ridimensionati nei tre esempi precedenti hanno tutti aumenta o diminuisce le dimensioni rispetto al centro dell'elemento. In altre parole, l'elemento aumenta o diminuisce le dimensioni lo stesso in tutte le direzioni. Durante il ridimensionamento, solo il punto al centro dell'elemento rimane nella stessa posizione.

È possibile modificare il centro della scala impostando le proprietà [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) . Queste proprietà sono rispetto all'elemento stesso. Per `AnchorX`, un valore pari a 0 si riferisce al lato sinistro dell'elemento e 1 si riferisce al lato destro. Analogamente, per `AnchorY`, 0 è il primo e il primo è il lato inferiore. Entrambe le proprietà hanno valori predefiniti pari a 0,5, che corrisponde al punto centrale.

L'esempio [**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) consente di sperimentare le proprietà `AnchorX` e `AnchorY`, nonché la proprietà `Scale`.

In iOS, l'utilizzo di valori non predefiniti di `AnchorX` e `AnchorY` proprietà è in genere incompatibile con le modifiche all'orientamento del telefono.

## <a name="the-rotation-transform"></a>La trasformazione di rotazione

La proprietà [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) è specificata in gradi e indica una rotazione in senso orario attorno a un punto dell'elemento definito da `AnchorX` e `AnchorY`. [**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) consente di sperimentare queste tre proprietà.

### <a name="rotated-text-effects"></a>Effetti di testo ruotato

L'esempio [**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) illustra la matematica necessaria per creare un cerchio usando gli elementi di `BoxView` ruotati di 64 piccoli.

L'esempio [**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) Visualizza più elementi `Label` con la stessa stringa di testo ruotata per apparire come spoke.

Nell'esempio [**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) viene visualizzata una stringa di testo che sembra eseguire il wrapping in un cerchio.

### <a name="an-analog-clock"></a>Un orologio analogico

La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una classe [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) che calcola gli angoli per le mani di un orologio. Per evitare le dipendenze della piattaforma nell'elemento ViewModel, la classe USA `Task.Delay` anziché un timer per trovare un nuovo valore `DateTime`.

Incluso anche in **Novell. FormsBook. Toolkit** è una classe [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) che implementa `IValueConverter` e serve per arrotondare un secondo angolo al secondo più vicino.

[**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) usa tre elementi di `BoxView` rotanti per creare un orologio analogico.

[**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) USA `BoxView` per grafici più estesi, inclusi i segni di graduazione intorno al quadrante dell'orologio e le mani che ruotano una certa distanza dalle estremità:

[![Schermata tripla del clock BoxView](images/ch21fg17-small.png "Quadrante orologio analogico")](images/ch21fg17-large.png#lightbox "Quadrante orologio analogico")

Inoltre, una classe [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) in **Novell. FormsBook. Toolkit** fa in modo che la seconda mano venga visualizzata prima di eseguire il pull prima del passaggio e quindi tornare alla posizione corretta.

### <a name="vertical-sliders"></a>Dispositivi di scorrimento verticale?

Nell'esempio [**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) viene illustrato che gli elementi `Slider` possono essere ruotati di 90 gradi e continuano a funzionare. Tuttavia, è difficile posizionare questi elementi di `Slider` ruotati perché nel layout sembrano ancora essere orizzontali.

## <a name="3d-ish-rotations"></a>Rotazioni 3D ICA

La proprietà [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) sembra ruotare un elemento intorno a un asse X 3D, in modo che la parte superiore e inferiore dell'elemento sembrino spostarsi verso o lontano dal visualizzatore. Analogamente, il [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) sembra ruotare un elemento intorno all'asse Y per fare in modo che il lato sinistro e destro dell'elemento sembrino spostarsi verso o lontano dal visualizzatore.

La proprietà `AnchorX` influiscono `RotationY` ma non `RotationX`. La proprietà `AnchorY` influiscono `RotationX` ma non `RotationY`. È possibile sperimentare l'esempio [**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) per esplorare le interazioni di queste proprietà.

Il sistema di coordinate 3D in cui è inclusa per xamarin. Forms è da sinistra. Se si punta l'indice della propria mano a sinistra nella direzione di incremento X coordina (a destra) e coordina il dito intermedio nella direzione y crescente (inattivo), quindi i punti di controllo thumb nella direzione crescente le coordinate Z (su schermo).

Inoltre, per uno dei tre assi, se si posiziona il cursore a sinistra nella direzione di aumentare i valori, quindi la curva delle dita indica la direzione di rotazione per gli angoli di rotazione positivi.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 21 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Capitolo 21 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
