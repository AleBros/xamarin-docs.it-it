---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 21. Transforms''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 32393108f84ea3a57079c86b6a9a8e628ceca03a
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136669"
---
# <a name="summary-of-chapter-21-transforms"></a>Riepilogo del capitolo 21. Trasformazioni

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Una Xamarin.Forms visualizzazione viene visualizzata sullo schermo in una posizione e in una dimensione determinate dall'elemento padre, che in genere è un oggetto `Layout` o `Layout<View>` derivato. La *trasformazione* è una Xamarin.Forms funzionalità che può modificare la posizione, le dimensioni o persino l'orientamento.

Xamarin.Formssupporta tre tipi di trasformazioni di base:

- *Traduzione* &mdash; in spostare un elemento orizzontalmente o verticalmente
- *Ridimensiona* &mdash; modificare le dimensioni di un elemento
- *Rotazione* &mdash; trasforma un elemento intorno a un punto o un asse

In Xamarin.Forms il ridimensionamento è di tipo di filtro di tipo e ha effetti sulla larghezza e sull'altezza in modo uniforme. La rotazione è supportata sia sulla superficie bidimensionale dello schermo che nello spazio 3D. Non esiste alcuna trasformazione inclinata (o trasparente) e nessuna trasformazione matrice generalizzata.

Le trasformazioni sono supportate con otto proprietà di tipo `double` definite dalla `VisualElement` classe:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Tutte queste proprietà sono supportate da proprietà associabili. Possono essere destinazioni di data binding e con stile. [**Capitolo 22. L'animazione**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) illustra il modo in cui queste proprietà possono essere animate, ma alcuni esempi in questo capitolo illustrano come è possibile animarli usando il Xamarin.Forms [timer](~/xamarin-forms/platform/device.md#devicestarttimer).

Le proprietà di trasformazione influiscono solo sulla modalità di rendering dell'elemento e *non* influiscono sul modo in cui l'elemento viene percepito nel layout.

## <a name="the-translation-transform"></a>Trasformazione della traduzione

I valori diversi da zero [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) delle [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) proprietà e spostano un elemento orizzontalmente o verticalmente.

Il programma [**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) consente di sperimentare queste proprietà con due `Slider` elementi che controllano le `TranslationX` proprietà e `TranslationY` di un oggetto `Frame` . La trasformazione influiscono anche su tutti gli elementi figlio `Frame` .

### <a name="text-effects"></a>Effetti di testo

Un uso comune delle proprietà di traduzione consiste nell'offset leggermente del rendering del testo. Questa operazione è illustrata nell'esempio [**TextOffsets**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) :

[![Schermata tripla degli offset di testo](images/ch21fg03-small.png "Offset di testo")](images/ch21fg03-large.png#lightbox "Offset di testo")

Un altro effetto consiste nel eseguire il rendering di più copie di un oggetto `Label` in modo analogo a un blocco 3D, come illustrato nell'esempio [**BlockText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) .

### <a name="jumps-and-animations"></a>Salti e animazioni

L'esempio [**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) usa la traduzione per spostare un oggetto `Button` ogni volta che viene toccato, ma lo scopo principale consiste nel dimostrare che `Button` riceve l'input dell'utente nella posizione in cui viene eseguito il rendering del pulsante.

L'esempio [**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) è simile, ma usa un timer per aggiungere un'animazione a `Button` da un punto a un altro.

## <a name="the-scale-transform"></a>Trasformazione scala

La [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) trasformazione può aumentare o diminuire la dimensione di cui è stato eseguito il rendering dell'elemento. Il valore predefinito è 1. Un valore di 0 fa sì che l'elemento sia invisibile. I valori negativi comportano la rotazione di 180 gradi dell'elemento. La `Scale` proprietà non influisce sulle `Width` `Height` proprietà o dell'elemento. Tali valori rimangono invariati.

È possibile sperimentare la `Scale` proprietà usando l'esempio [**SimpleScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) .

Nell'esempio [**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) viene illustrata la differenza tra l'animazione della `Scale` proprietà di un oggetto `Button` e l'animazione della `FontSize` Proprietà. La `FontSize` proprietà influiscono sul modo in cui l'oggetto `Button` viene percepito nel layout; la `Scale` proprietà non.

Nell'esempio [**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) viene calcolata una `Scale` proprietà che viene applicata a un `Label` elemento per renderla più grande possibile mentre è ancora adatta all'interno della pagina.

### <a name="anchoring-the-scale"></a>Ancoraggio della scala

Gli elementi ridimensionati nei tre campioni precedenti sono stati aumentati o ridotti di dimensione rispetto al centro dell'elemento. In altre parole, l'elemento aumenta o diminuisce di dimensione lo stesso in tutte le direzioni. Solo il punto al centro dell'elemento rimane nella stessa posizione durante il ridimensionamento.

È possibile modificare il centro del ridimensionamento impostando le [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) proprietà e. Queste proprietà sono relative all'elemento stesso. Per `AnchorX` , un valore pari a 0 si riferisce al lato sinistro dell'elemento e 1 si riferisce al lato destro. Analogamente per `AnchorY` , 0 è il primo e 1 è il lato inferiore. Entrambe le proprietà hanno valori predefiniti 0,5, che corrisponde al centro.

L'esempio [**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) consente di sperimentare le `AnchorX` proprietà e, nonché `AnchorY` la `Scale` Proprietà.

In iOS, l'utilizzo di valori non predefiniti `AnchorX` delle `AnchorY` proprietà e è generalmente incompatibile con le modifiche all'orientamento del telefono.

## <a name="the-rotation-transform"></a>Trasformazione di rotazione

La [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) proprietà viene specificata in gradi e indica una rotazione in senso orario intorno a un punto dell'elemento definito da `AnchorX` e `AnchorY` . [**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) consente di sperimentare queste tre proprietà.

### <a name="rotated-text-effects"></a>Effetti di testo ruotati

L'esempio [**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) illustra la matematica necessaria per creare un cerchio usando gli elementi ruotati di 64 piccoli `BoxView` .

L'esempio [**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) Visualizza più `Label` elementi con la stessa stringa di testo ruotata per apparire come spoke.

Nell'esempio [**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) viene visualizzata una stringa di testo che sembra eseguire il wrapping in un cerchio.

### <a name="an-analog-clock"></a>Un orologio analogico

La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) classe che calcola gli angoli per le mani di un orologio. Per evitare le dipendenze della piattaforma nell'elemento ViewModel, la classe USA `Task.Delay` invece di un timer per trovare un nuovo `DateTime` valore.

Incluso anche in **Novell. FormsBook. Toolkit** è una [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) classe che implementa `IValueConverter` e serve per arrotondare un secondo angolo al secondo più vicino.

[**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) usa tre elementi rotanti `BoxView` per creare un orologio analogico.

Il [**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) USA `BoxView` per la grafica più estesa, inclusi i segni di graduazione intorno al quadrante dell'orologio e le mani che ruotano una certa distanza dalle estremità:

[![Schermata tripla del clock BoxView](images/ch21fg17-small.png "Quadrante orologio analogico")](images/ch21fg17-large.png#lightbox "Quadrante orologio analogico")

Inoltre, una [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) classe in **Novell. FormsBook. Toolkit** fa in modo che la seconda mano venga visualizzata prima di eseguire il pull prima del passaggio e quindi tornare alla posizione corretta.

### <a name="vertical-sliders"></a>Dispositivi di scorrimento verticali?

L'esempio [**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) illustra che `Slider` gli elementi possono essere ruotati di 90 gradi e continuano a funzionare. Tuttavia, è difficile posizionare questi elementi ruotati `Slider` perché nel layout sembrano ancora essere orizzontali.

## <a name="3d-ish-rotations"></a>rotazioni 3D-ish

La [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) proprietà viene visualizzata per ruotare un elemento intorno a un asse X 3D in modo che la parte superiore e inferiore dell'elemento sembrino spostarsi verso o lontano dal visualizzatore. Analogamente, il [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) sembra ruotare un elemento intorno all'asse Y per fare in modo che il lato sinistro e destro dell'elemento sembrino spostarsi verso o lontano dal visualizzatore.

La `AnchorX` proprietà ha effetto `RotationY` ma non `RotationX` . La `AnchorY` proprietà ha effetto `RotationX` ma non `RotationY` . È possibile sperimentare l'esempio [**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) per esplorare le interazioni di queste proprietà.

Il sistema di coordinate 3D implicito Xamarin.Forms è a sinistra. Se si posiziona l'indice della mano sinistra nella direzione di aumentare le coordinate X (a destra) e il dito medio nella direzione di aumentare le coordinate Y (in basso), il cursore punta alla direzione di aumentare le coordinate Z (sullo schermo).

Inoltre, per uno dei tre assi, se si posiziona il cursore a sinistra nella direzione dell'aumento dei valori, la curva delle dita indica la direzione della rotazione per gli angoli di rotazione positivi.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 21 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Capitolo 21 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
