---
title: Riassunto del capitolo 21. Trasformazioni
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 21. Trasformazioni'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760573"
---
# <a name="summary-of-chapter-21-transforms"></a>Riassunto del capitolo 21. Trasformazioni

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Una vista Xamarin.Forms viene visualizzata sullo schermo in una posizione `Layout` e `Layout<View>` una dimensione determinate dal relativo elemento padre, che in genere è a o derivata. La *trasformazione* è una funzione Xamarin.Forms che può modificare tale posizione, dimensione o anche orientamento.

Xamarin.Forms supporta tre tipi di trasformazioni di base:

- *La traslazione* &mdash; sposta un elemento orizzontalmente o verticalmente
- *Modificare* &mdash; le dimensioni di un elemento
- *Rotazione* &mdash; ruotare un elemento attorno a un punto o un asse

In Xamarin.Forms, il ridimensionamento è isotropico; influisce uniformemente sulla larghezza e sull'altezza. La rotazione è supportata sia sulla superficie bidimensionale dello schermo che nello spazio 3D. Non esiste alcuna trasformazione di inclinazione (o pura) e nessuna trasformazione di matrice generalizzata.

Le trasformazioni sono supportate `double` da `VisualElement` otto proprietà di tipo definite dalla classe:Transforms are supported with eight properties of type defined by the class:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Tutte queste proprietà sono supportate da proprietà associabili. Possono essere destinazioni dell'associazione dati e stili. [**Capitolo 22. L'animazione**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) dimostra come queste proprietà possono essere animate, ma alcuni esempi in questo capitolo mostrano come è possibile animarle utilizzando il [timer](~/xamarin-forms/platform/device.md#devicestarttimer)Xamarin.Forms .

Le proprietà di trasformazione influiscono solo sul modo in cui viene eseguito il rendering dell'elemento e *non* sul modo in cui l'elemento viene percepito nel layout.

## <a name="the-translation-transform"></a>La trasformazione di traslazione

I valori diversi [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) da zero delle proprietà e spostano un elemento orizzontalmente o verticalmente.

Il programma [**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) consente di sperimentare `Slider` queste proprietà `TranslationX` con `TranslationY` due `Frame`elementi che controllano le proprietà e di un oggetto . La trasformazione interessa anche `Frame`tutti gli elementi figlio di tale .

### <a name="text-effects"></a>Effetti di testo

Un utilizzo comune delle proprietà di traslazione consiste nell'eseguire leggermente l'offset del rendering del testo. Ciò è illustrato nell'esempio [**TextOffsets:This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) is demonstrated in the TextOffsets sample:

[![Tripla schermata degli scostamenti di testo](images/ch21fg03-small.png "Offset del testo")](images/ch21fg03-large.png#lightbox "Offset del testo")

Un altro effetto consiste nel `Label` eseguire il rendering di più copie di un oggetto per essere simile a un blocco 3D, ad esempio illustrato nell'esempio [**BlockText.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText)

### <a name="jumps-and-animations"></a>Salti e animazioni

Il [**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) esempio usa `Button` la conversione per spostare un ogni volta `Button` che viene toccato, ma lo scopo principale è quello di dimostrare che riceve l'input dell'utente nella posizione in cui viene eseguito il rendering del pulsante.

Il [**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) esempio è simile, ma `Button` utilizza un timer per animare il da un punto a un altro.

## <a name="the-scale-transform"></a>Trasformazione della scala

La [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) trasformazione può aumentare o ridurre le dimensioni di rendering dell'elemento. Il valore predefinito è 1. Un valore pari a 0 fa sì che l'elemento sia invisibile. I valori negativi fanno sì che l'elemento sembri ruotare di 180 gradi. La `Scale` proprietà non `Width` influisce `Height` sulle proprietà o dell'elemento. Questi valori rimangono invariati.

È possibile sperimentare `Scale` la proprietà utilizzando l'esempio [**SimpleScaleDemo.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo)

[**Nell'esempio ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) viene illustrata `Scale` la `Button` differenza tra `FontSize` l'animazione della proprietà di a e l'animazione della proprietà. La `FontSize` proprietà influisce `Button` sul modo in cui l'oggetto viene percepito nel layout; la `Scale` proprietà non lo fa.

L'esempio [**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) `Scale` calcola una proprietà `Label` applicata a un elemento per renderlo il più grande possibile, pur rimanendo adattandolo all'interno della pagina.

### <a name="anchoring-the-scale"></a>Ancoraggio della bilancia

Gli elementi scalati nei tre campioni precedenti sono tutti aumentati o diminuiti di dimensioni rispetto al centro dell'elemento. In altre parole, l'elemento aumenta o diminuisce di dimensioni uguali in tutte le direzioni. Solo il punto al centro dell'elemento rimane nella stessa posizione durante il ridimensionamento.

È possibile modificare il centro del [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) ridimensionamento impostando le proprietà e . Queste proprietà sono relative all'elemento stesso. Per `AnchorX`, il valore 0 si riferisce al lato sinistro dell'elemento e 1 si riferisce al lato destro. Allo stesso `AnchorY`modo per , 0 è la parte superiore e 1 è il fondo. Entrambe le proprietà hanno valori predefiniti pari a 0,5, ovvero il centro.

Il [**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) esempio consente di `AnchorX` `AnchorY` sperimentare le proprietà `Scale` e nonché la proprietà .

In iOS, l'uso `AnchorX` di `AnchorY` valori e proprietà non predefiniti è in genere incompatibile con le modifiche di orientamento del telefono.

## <a name="the-rotation-transform"></a>La trasformazione di rotazione

La [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) proprietà viene specificata in gradi e indica la rotazione in `AnchorX` `AnchorY`senso orario intorno a un punto dell'elemento definito da e . [**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) consente di sperimentare con queste tre proprietà.

### <a name="rotated-text-effects"></a>Effetti di testo ruotati

Il [**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) esempio viene illustrata la matematica necessaria per `BoxView` disegnare un cerchio utilizzando 64 piccoli elementi ruotati.

Nell'esempio [**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) vengono visualizzati più `Label` elementi con la stessa stringa di testo ruotata in modo da apparire come raggi.

Nell'esempio [**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) viene visualizzata una stringa di testo che sembra andare a capo in un cerchio.

### <a name="an-analog-clock"></a>Un orologio analogico

La libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) classe che calcola gli angoli per le lancette di un orologio. Per evitare dipendenze della piattaforma nel `Task.Delay` ViewModel, la classe `DateTime` utilizza anziché un timer per trovare un nuovo valore.

In **Xamarin.FormsBook.Toolkit** è [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) inclusa `IValueConverter` anche una classe che implementa e serve ad arrotondare un secondo angolo al secondo più vicino.

Il [**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) utilizza `BoxView` tre elementi rotanti per disegnare un orologio analogico.

Il [**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) utilizza `BoxView` per una grafica più estesa, compresi i segni di graduazione intorno al quadrante dell'orologio e le lancette che ruotano a poca distanza dalle loro estremità:

[![Tripla schermata di BoxView Clock](images/ch21fg17-small.png "Quadrante dell'orologio analogico")](images/ch21fg17-large.png#lightbox "Quadrante dell'orologio analogico")

Inoltre una [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) classe in **Xamarin.FormsBook.Toolkit** fa sì che la lancetta dei secondi sembri tirare indietro un po 'prima di saltare in avanti, e poi tornare nella sua posizione corretta.

### <a name="vertical-sliders"></a>Cursori verticali?

Il [**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) esempio `Slider` dimostra che gli elementi possono essere ruotati di 90 gradi e ancora funzionare. Tuttavia, è difficile posizionare `Slider` questi elementi ruotati perché nel layout sembrano essere ancora orizzontali.

## <a name="3d-ish-rotations"></a>Rotazioni 3D-ish

La [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) proprietà sembra ruotare un elemento intorno a un asse X 3D in modo che la parte superiore e inferiore dell'elemento sembri spostarsi verso o lontano dal visualizzatore. Allo stesso [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) modo, l'oggetto sembra ruotare un elemento intorno all'asse Y per fare in modo che i lati sinistro e destro dell'elemento sembrino spostarsi verso o lontano dallo spettatore.

La `AnchorX` proprietà `RotationY` ha `RotationX`effetto ma non . La `AnchorY` proprietà `RotationX` ha `RotationY`effetto ma non . È possibile sperimentare con il [**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) esempio per esplorare le interazioni di queste proprietà.

Il sistema di coordinate 3D implicito da Xamarin.Forms è mancino. Se si punta l'indice della mano sinistra nella direzione dell'aumento delle coordinate X (a destra) e il dito medio nella direzione delle coordinate Y crescenti (verso il basso), il pollice punta nella direzione di aumentare le coordinate di z (fuori dallo schermo).

Inoltre, per uno qualsiasi dei tre assi, se si punta il pollice sinistro nella direzione di valori crescenti, la curva delle dita indica la direzione di rotazione per gli angoli rotanti positivi.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 21 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Esempi del capitolo 21](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
