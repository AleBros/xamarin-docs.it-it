---
title: Riepilogo del capitolo 5. Gestione delle dimensioni
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 5. Gestione delle dimensioni'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771147"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Riepilogo del capitolo 5. Gestione delle dimensioni

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

Diverse dimensioni in xamarin. Forms sono state rilevate fino a questo momento:

- L'altezza della barra di stato iOS è 20
- Il `BoxView` ha una larghezza e un'altezza predefinite di 40
- Il `Padding` predefinito in una `Frame` è 20
- Il `Spacing` predefinito sul `StackLayout` è 6
- Il metodo `Device.GetNamedSize` restituisce una dimensione numerica del tipo di carattere

Queste dimensioni non sono i pixel. In questo caso sono unità indipendenti dal dispositivo riconosciute in modo indipendente per ogni piattaforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixel, punti, punti di distribuzione, DIP e DIUs

Nelle prime fasi di cronologie di Apple Mac e Microsoft Windows, i programmatori funzionava in unità di pixel. Tuttavia, l'avvento degli schermi con risoluzione superiore è necessario un approccio più virtualizzato e astratto in coordinate dello schermo. Nel mondo Mac, i programmatori lavoravano in unità di *punti*, tradizionalmente 1/72 pollice, mentre gli sviluppatori Windows usavano *unità indipendenti dal dispositivo* (DIUs) basate su 1/96 pollice.

I dispositivi mobili, tuttavia, vengono mantenuti in genere molto più vicino per il viso e hanno una risoluzione maggiore rispetto a desktop le schermate, che implica che è possibile tollerare una maggiore densità in pixel.

I programmatori che hanno come destinazione dispositivi Apple iPhone e iPad continuano a funzionare in unità di *punti*, ma sono presenti 160 di questi punti al pollice. A seconda del dispositivo, potrebbe essere 1, 2 o 3 pixel per il punto.

Android è simile. I programmatori operano in unità di *pixel indipendenti dalla densità* (DPS) e la relazione tra DPS e pixel si basa su 160 DPS al pollice.

I telefoni Windows e dispositivi mobili anche aver stabilito i fattori di scala che implicano qualcosa di simile a 160 unità indipendenti dal dispositivo di pollice.

> [!NOTE]
> Xamarin. Forms non supporta più qualsiasi phone basate su Windows o un dispositivo mobile.

In sintesi, un programmatore di xamarin. Forms destinate a telefoni e Tablet possa presupporre che tutte le unità di misura sono basate sul criterio seguente:

- 160 unità per pollice, equivalente a
- unità di 64 per il centimetro

Il [`Width`](xref:Xamarin.Forms.VisualElement.Width) di sola lettura e le proprietà [`Height`](xref:Xamarin.Forms.VisualElement.Height) definite da `VisualElement` hanno valori "fittizi" predefiniti di &ndash;1. Solo quando un elemento è stato ridimensionato e gestito nei layout di queste proprietà riflette le dimensioni effettive dell'elemento in unità indipendenti dal dispositivo. Questa dimensione include tutti `Padding` impostati sull'elemento ma non sull'`Margin`.

Un elemento visivo genera l'evento [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) quando il relativo `Width` o `Height` è stato modificato. L'esempio [**WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) utilizza questo evento per visualizzare le dimensioni della schermata del programma.

## <a name="metrical-sizes"></a>Dimensioni di metrica:

[**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) USA [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) per visualizzare un `BoxView` un pollice di altezza e un centimetro di larghezza.

## <a name="estimated-font-sizes"></a>Dimensioni dei caratteri stimato

Nell'esempio [**fontsizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) viene illustrato come utilizzare la regola 160-unità-pollice per specificare le dimensioni del carattere in unità di punti. La coerenza visiva tra le piattaforme che usano questa tecnica è migliore della `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Adattamento del testo di dimensioni disponibili

È possibile adattare un blocco di testo all'interno di un particolare rettangolo calcolando una `FontSize` del `Label` utilizzando i criteri seguenti:

- L'interlinea è 120% delle dimensioni del carattere (130% nelle piattaforme Windows).
- Media di un carattere width è 50% delle dimensioni del carattere.

Questa tecnica è illustrata nell'esempio [**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) . Questo programma è stato scritto prima della disponibilità della proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) , quindi usa una [`ContentView`](xref:Xamarin.Forms.ContentView) con un'impostazione [`Padding`](xref:Xamarin.Forms.Layout.Padding) per simulare un margine.

[![Schermata tripla delle dimensioni stimate del carattere](images/ch05fg07-small.png "Testo adatto alle dimensioni disponibili")](images/ch05fg07-large.png#lightbox "Testo adatto alle dimensioni disponibili")

## <a name="a-fit-to-size-clock"></a>Un orologio fit-a-dimensioni

Nell'esempio [**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) viene illustrato l'uso di [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) per avviare un timer che notifica periodicamente all'applicazione quando è il momento di aggiornare il clock. Le dimensioni del carattere sono impostata su un-sesto della larghezza della pagina per rendere lo schermo di dimensioni corrispondenti.

## <a name="accessibility-issues"></a>Problemi di accessibilità

Il programma **EstimatedFontSize** e il programma **FitToSizeClock** contengono entrambi un lieve difetto: se l'utente modifica le impostazioni di accessibilità del telefono in Android o Windows 10 Mobile, il programma non è più in grado di stimare il rendering del testo in base alle dimensioni del carattere. Questo problema viene illustrato nell'esempio [**AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) .

## <a name="empirically-fitting-text"></a>In modo empirico adattamento del testo

Un altro modo per adattare il testo a un rettangolo è in modo empirico calcolare le dimensioni del testo sottoposto a rendering e modificarlo verso l'alto o verso il basso. Il programma nel libro chiama [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) su un elemento visivo per ottenere le dimensioni desiderate dell'elemento. Questo metodo è stato deprecato e i programmi devono chiamare invece [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)).

Per un `Label`, il primo argomento deve essere la larghezza del contenitore (per consentire il wrapping), mentre il secondo argomento deve essere impostato su `Double.PositiveInfinity` per rendere l'altezza non vincolata. Questa tecnica è illustrata nell'esempio [**EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) .

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 5 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Esempi del capitolo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Esempi del F# capitolo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
