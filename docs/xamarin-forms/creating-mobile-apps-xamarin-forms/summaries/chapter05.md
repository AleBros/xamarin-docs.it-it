---
title: Riepilogo del capitolo 5. Gestione delle dimensioni
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 5. Gestione delle dimensioni'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 37403cfe9f37972c20fb074db5e30cc54b60fea9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136877"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Riepilogo del capitolo 5. Gestione delle dimensioni

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Le note in questa pagina indicano Xamarin.Forms le aree in cui è stato divergente rispetto al materiale presentato nel libro.

Xamarin.FormsFino a questo punto sono state rilevate diverse dimensioni in:

- L'altezza della barra di stato di iOS è 20
- `BoxView`Ha una larghezza e un'altezza predefinite di 40
- Il valore predefinito `Padding` in a `Frame` è 20
- Il valore predefinito `Spacing` di `StackLayout` è 6
- Il `Device.GetNamedSize` metodo restituisce una dimensione numerica del tipo di carattere

Queste dimensioni non sono pixel. Sono invece unità indipendenti dal dispositivo riconosciute in modo indipendente da ogni piattaforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixel, punti, DPS, DIP e DIUs

Nelle cronologie di Apple Mac e Microsoft Windows, i programmatori lavoravano in unità di pixel. Tuttavia, l'avvento degli schermi con risoluzione superiore richiede un approccio più virtualizzato e astratto per le coordinate dello schermo. Nel mondo Mac, i programmatori lavoravano in unità di *punti*, tradizionalmente 1/72 pollice, mentre gli sviluppatori Windows usavano *unità indipendenti dal dispositivo* (DIUs) basate su 1/96 pollice.

I dispositivi mobili, tuttavia, sono in genere molto più vicini alla faccia e hanno una risoluzione più elevata rispetto alle schermate desktop, il che significa che è possibile tollerare una maggiore densità di pixel.

I programmatori che hanno come destinazione dispositivi Apple iPhone e iPad continuano a funzionare in unità di *punti*, ma sono presenti 160 di questi punti al pollice. A seconda del dispositivo, il punto può essere 1, 2 o 3 pixel.

Android è simile. I programmatori operano in unità di *pixel indipendenti dalla densità* (DPS) e la relazione tra DPS e pixel si basa su 160 DPS al pollice.

I dispositivi mobili e i telefoni Windows hanno anche stabilito fattori di scalabilità che implicano un valore simile a 160 unità indipendenti dal dispositivo a pollice.

> [!NOTE]
> Xamarin.Formsnon supporta più dispositivi mobili o telefoni basati su Windows.

In sintesi, un Xamarin.Forms programmatore destinato a telefoni e tablet può presupporre che tutte le unità di misura siano basate sul criterio seguente:

- 160 unità al pollice, equivalenti a
- 64 unità al centimetro

Le proprietà di sola lettura [`Width`](xref:Xamarin.Forms.VisualElement.Width) e [`Height`](xref:Xamarin.Forms.VisualElement.Height) definite da `VisualElement` hanno valori predefiniti di "mock" pari a &ndash; 1. Queste proprietà riflettono le dimensioni effettive dell'elemento in unità indipendenti dal dispositivo solo quando un elemento è stato ridimensionato e è stato adattato al layout. Questa dimensione include tutti i `Padding` set nell'elemento, ma non `Margin` .

Un elemento visivo genera l' [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) evento quando `Width` `Height` viene modificato o. L'esempio [**WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) utilizza questo evento per visualizzare le dimensioni della schermata del programma.

## <a name="metrical-sizes"></a>Dimensioni metriche

Il [**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) USA [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) per visualizzare un `BoxView` pollice di altezza e un centimetro di larghezza.

## <a name="estimated-font-sizes"></a>Dimensioni stimate dei caratteri

Nell'esempio [**fontsizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) viene illustrato come utilizzare la regola 160-unità-pollice per specificare le dimensioni del carattere in unità di punti. La coerenza visiva tra le piattaforme che usano questa tecnica è preferibile a `Device.GetNamedSize` .

## <a name="fitting-text-to-available-size"></a>Adattamento del testo alla dimensione disponibile

È possibile adattare un blocco di testo all'interno di un particolare rettangolo calcolando un oggetto `FontSize` `Label` utilizzando i criteri seguenti:

- L'interlinea è pari al 120% delle dimensioni del carattere (130% sulle piattaforme Windows).
- La larghezza media dei caratteri corrisponde al 50% delle dimensioni del carattere.

Questa tecnica è illustrata nell'esempio [**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) . Questo programma è stato scritto prima della [`Margin`](xref:Xamarin.Forms.View.Margin) disponibilità della proprietà, quindi usa un oggetto [`ContentView`](xref:Xamarin.Forms.ContentView) con un' [`Padding`](xref:Xamarin.Forms.Layout.Padding) impostazione per simulare un margine.

[![Schermata tripla delle dimensioni stimate del carattere](images/ch05fg07-small.png "Testo adatto alle dimensioni disponibili")](images/ch05fg07-large.png#lightbox "Testo adatto alle dimensioni disponibili")

## <a name="a-fit-to-size-clock"></a>Un clock adatta alle dimensioni

Nell'esempio [**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) viene illustrato l'utilizzo [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) di per avviare un timer che notifica periodicamente all'applicazione quando è il momento di aggiornare l'orologio. La dimensione del carattere è impostata su un sesto della larghezza della pagina per rendere la visualizzazione il più grande possibile.

## <a name="accessibility-issues"></a>Problemi di accessibilità

Il programma **EstimatedFontSize** e il programma **FitToSizeClock** contengono entrambi un lieve difetto: se l'utente modifica le impostazioni di accessibilità del telefono in Android o Windows 10 Mobile, il programma non è più in grado di stimare il rendering del testo in base alle dimensioni del carattere. Questo problema viene illustrato nell'esempio [**AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) .

## <a name="empirically-fitting-text"></a>Adattamento empirico del testo

Un altro modo per adattare il testo a un rettangolo consiste nel calcolare empiricamente le dimensioni del testo di cui è stato eseguito il rendering e adattarlo. Il programma nel libro chiama [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) su un elemento visivo per ottenere le dimensioni desiderate dell'elemento. Questo metodo è stato deprecato e i programmi devono chiamare invece [ `Measure` ] (xrif: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags)).

Per `Label` , il primo argomento deve essere la larghezza del contenitore (per consentire il wrapping), mentre il secondo argomento deve essere impostato su `Double.PositiveInfinity` per rendere l'altezza non vincolata. Questa tecnica è illustrata nell'esempio [**EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) .

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 5 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Esempi del capitolo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Capitolo 5 esempi di F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
