---
title: Riepilogo del capitolo 21. Trasformazioni
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: a40b4f00fd2a0dd3f61c4882a2ef25b8eb68a3a5
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-21-transforms"></a>Riepilogo del capitolo 21. Trasformazioni

Verrà visualizzata una visualizzazione di xamarin. Forms sullo schermo in una posizione e dimensione determinata dal relativo elemento padre, che corrisponde in genere un `Layout` o `Layout<View>` derivato. Il *trasformare* è una funzionalità di xamarin. Forms che è possibile modificare tale posizione, dimensione o orientamento anche.

Xamarin. Forms supporta tre tipi di base di trasformazioni:

- *Conversione* &mdash; spostare un elemento orizzontalmente o verticalmente
- *Scala* &mdash; modificare le dimensioni di un elemento
- *Rotazione* &mdash; attivare un elemento intorno a un punto o un asse

In xamarin. Forms, il ridimensionamento è isotropico; influisce su larghezza e altezza in modo uniforme. La rotazione è supportata sia nella superficie bidimensionale dello schermo e nello spazio 3D. Non vi è alcuna trasformazione di inclinazione (o determinato dal) e Nessuna trasformazione di matrice generalizzato.

Le trasformazioni sono supportate con otto proprietà di tipo `double` definito dalla `VisualElement` classe:

- [`TranslationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)
- [`TranslationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)
- [`Scale`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)
- [`Rotation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)
- [`RotationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/)
- [`RotationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/)
- [`AnchorX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)
- [`AnchorY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)

Tutte queste proprietà sono supportate da proprietà associabili. Possono essere destinazioni di associazione dati e lo stile. [**Capitolo 22. Animazione** ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) viene illustrato come queste proprietà possono essere animate, ma alcuni esempi in questo capitolo viene illustrato come è possibile animare mediante il xamarin. Forms [timer](~/xamarin-forms/platform/device.md#Device_StartTimer).

Trasformare proprietà hanno effetto solo come l'elemento viene sottoposto a rendering e si *non* influiscono su come l'elemento viene considerato nel layout.

## <a name="the-translation-transform"></a>La trasformazione di conversione

Valori diversi da zero del [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) e [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) proprietà spostare un elemento orizzontalmente o verticalmente.

Il [ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) programma consente di sperimentare queste proprietà con due `Slider` elementi che controllano il `TranslationX` e `TranslationY` le proprietà di un `Frame`. La trasformazione influisce anche tutti gli elementi figlio di tale `Frame`.

### <a name="text-effects"></a>Effetti del testo

Un utilizzo comune di proprietà di conversione è fino all'offset leggermente il rendering del testo. Questa funzionalità viene illustrata la [ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) esempio:

[![Schermata di testo viene eseguito l'offset di tripla](images/ch21fg03-small.png "testo viene eseguito l'offset")](images/ch21fg03-large.png#lightbox "testo viene eseguito l'offset")

Un altro effetto consiste nell'eseguire il rendering di più copie di un `Label` simile a quella di un blocco 3D, come illustrato nel [ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) esempio.

### <a name="jumps-and-animations"></a>Collegamenti e le animazioni

Il [ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) esempio utilizza una traduzione per spostare un `Button` ogni volta che verrà scelti, ma lo scopo principale consiste nel dimostrare che il `Button` riceve l'input dell'utente in corrispondenza della posizione in cui viene visualizzato il pulsante.

Il [ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) di esempio è simile ma utilizza un timer per animare la `Button` da un punto a altro.

## <a name="the-scale-transform"></a>La trasformazione di scala

Il [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) trasformazione è possibile aumentare o ridurre la dimensione di rendering dell'elemento. Il valore predefinito è 1. Un valore pari a 0, l'elemento sia invisibile. I valori negativi determinano l'elemento sembrino essere ruotata di 180 gradi. Il `Scale` non influisce sulla proprietà di `Width` o `Height` delle proprietà dell'elemento. Tali valori sono uguali.

È possibile sperimentare il `Scale` proprietà utilizzando il [ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) esempio.

Il [ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) esempio viene illustrata la differenza tra l'animazione di `Scale` proprietà di un `Button` e animazione di `FontSize` proprietà. Il `FontSize` proprietà ha effetto sul modo in cui il `Button` viene percepita nel layout; il `Scale` proprietà non.

Il [ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) esempio calcola un `Scale` proprietà a cui è applicato un `Label` elemento per renderlo più grande possibile mentre si adatti all'interno della pagina.

### <a name="anchoring-the-scale"></a>L'ancoraggio della scala

Gli elementi ridimensionati nei tre esempi precedenti sono tutti aumentato o diminuito dimensioni rispetto al centro dell'elemento. In altre parole, l'elemento aumenta o diminuisce le dimensioni, lo stesso in tutte le direzioni. Solo il punto al centro dell'elemento rimane nella stessa posizione durante il ridimensionamento.

È possibile modificare il centro della scala impostando il [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) e [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) proprietà. Queste proprietà si applicano all'elemento stesso. Per `AnchorX`, il lato sinistro dell'elemento si intende un valore pari a 0 e 1 si riferisce al lato destro. Analogamente, per `AnchorY`, 0 è la parte superiore e inferiore è 1. Entrambe le proprietà hanno valori predefiniti pari a 0,5, ovvero il centro.

Il [ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) esempio consente di sperimentare il `AnchorX` e `AnchorY` proprietà, nonché `Scale` proprietà.

In iOS, utilizzando i valori non predefiniti di `AnchorX` e `AnchorY` proprietà non è in genere è compatibile con le modifiche di orientamento phone.

## <a name="the-rotation-transform"></a>La trasformazione di rotazione

Il [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) proprietà viene specificata in gradi e indica una rotazione in senso orario intorno a un punto dell'elemento definito da `AnchorX` e `AnchorY`. Il [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) consente di sperimentare queste tre proprietà.

### <a name="rotated-text-effects"></a>Effetti di testo ruotato

Il [ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) illustra i calcoli necessari per disegnare un cerchio utilizzando 64 piccoli ruotata `BoxView` elementi.

Il [ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) nell'esempio vengono visualizzati più `Label` ruotare gli elementi con la stessa stringa di testo viene visualizzato come spoke.

Il [ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) esempio consente di visualizzare una stringa di testo che viene visualizzato per eseguire il wrapping in un cerchio.

### <a name="an-analog-clock"></a>Un orologio analogico

Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria contiene un [ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) classe che calcola gli angoli per gli indicatori di un orologio. Per evitare le dipendenze della piattaforma nel ViewModel, la classe utilizza `Task.Delay` anziché un timer per la ricerca di un nuovo `DateTime` valore.

Incluso anche in **Xamarin.FormsBook.Toolkit** è un [ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) classe che implementa `IValueConverter` e consente di arrotondare un angolo secondo al secondo più vicino.

Il [ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) utilizza tre rotazione `BoxView` elementi per disegnare un orologio analogico.

Il [ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) utilizza `BoxView` per più molti elementi grafici, inclusi i segni di graduazione quadrante dell'orologio tra virgolette e consegna che ruota una distanza minima da loro estremità:

[![Schermata tripla dell'orologio BoxView](images/ch21fg17-small.png "quadrante dell'orologio analogico")](images/ch21fg17-large.png#lightbox "quadrante dell'orologio analogico")

Inoltre un [ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) classe **Xamarin.FormsBook.Toolkit** fa sì che l'icona della mano secondo da visualizzare per il pull back un po' prima di passare rapidamente e quindi di tornare nella posizione corretta.

### <a name="vertical-sliders"></a>Dispositivi di scorrimento verticale?

Il [ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) esempio viene dimostrato che `Slider` elementi possono essere ruotati di 90 gradi e continuano a funzionare. Tuttavia, è difficile posizionare queste ruotata `Slider` elementi poiché layout sono ancora sembrano essere orizzontale.

## <a name="3d-ish-rotations"></a>Rotazioni 3D ICA

Il [ `RotationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/) sembra ruota un elemento intorno a un asse x 3D in modo che la parte superiore e inferiore dell'elemento sembra per spostare verso o da Visualizzatore proprietà. Analogamente, il [ `RotationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/) sembra ruota un elemento intorno all'asse y per rendere i lati sinistro e destro dell'elemento sembra per spostare verso o da Visualizzatore.

Il `AnchorX` proprietà influisce su `RotationY` ma non `RotationX`. Il `AnchorY` proprietà influisce su `RotationX` ma non `RotationY`. È possibile sperimentare il [ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) esempio per esplorare le interazioni di queste proprietà.

Il sistema di coordinate 3D in cui è inclusa xamarin. Forms è da sinistra. Se si posiziona il indice della mano sinistra nella direzione di incremento X coordinate coordinate (a destra) e il dito intermedio nella direzione di incremento Y (punti verso il basso), quindi il casella di scorrimento nella direzione di aumentare le coordinate Z (fuori schermo).

Inoltre, per uno dei tre assi, se si posiziona il cursore a sinistra nella direzione di aumentare i valori, quindi la curva delle dita indica la direzione di rotazione per gli angoli di rotazione positivo.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 21 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Esempi di capitolo 21](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
