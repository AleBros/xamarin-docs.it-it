---
title: Riepilogo del capitolo 5. Gestione delle dimensioni
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: Riepilogo del capitolo 5. Gestione delle dimensioni'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771147"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Riepilogo del capitolo 5. Gestione delle dimensioni

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui Xamarin.Forms è diversa dal materiale presentato nel libro.

Diverse dimensioni in Xamarin.Forms sono state rilevate fino a questo momento:

- L'altezza della barra di stato iOS è 20
- Il `BoxView` ha una larghezza predefinita e un'altezza pari a 40
- Il valore predefinito `Padding` in un `Frame` è 20
- Il valore predefinito `Spacing` nella `StackLayout` è 6
- Il `Device.GetNamedSize` metodo restituisce una dimensione del carattere numerico

Queste dimensioni non sono i pixel. In questo caso sono unità indipendenti dal dispositivo riconosciute in modo indipendente per ogni piattaforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixel, punti, punti di distribuzione, DIP e DIUs

Nelle prime fasi di cronologie di Apple Mac e Microsoft Windows, i programmatori funzionava in unità di pixel. Tuttavia, l'avvento degli schermi con risoluzione superiore è necessario un approccio più virtualizzato e astratto in coordinate dello schermo. Nel mondo Mac, i programmatori ha lavorato in unità pari a *punti*, in genere 1/72 pollice, mentre gli sviluppatori di Windows utilizzato *device independent unit* (DIUs) basato su 1/96 di pollice.

I dispositivi mobili, tuttavia, vengono mantenuti in genere molto più vicino per il viso e hanno una risoluzione maggiore rispetto a desktop le schermate, che implica che è possibile tollerare una maggiore densità in pixel.

I programmatori destinate a dispositivi iPhone e iPad di Apple continuano a funzionare in unità pari *punti*, ma esistono 160 di questi punti per pollice. A seconda del dispositivo, potrebbe essere 1, 2 o 3 pixel per il punto.

Android è simile. I programmatori lavorano in unità pari *pixel indipendenti dalla densità* (dps), e si basa la relazione tra pixel e punti di distribuzione nel servizio Device 160 provisioning per pollice.

I telefoni Windows e dispositivi mobili anche aver stabilito i fattori di scala che implicano qualcosa di simile a 160 unità indipendenti dal dispositivo di pollice.

> [!NOTE]
> Xamarin.Forms non supporta più qualsiasi phone basate su Windows o un dispositivo mobile.

In sintesi, un programmatore di Xamarin.Forms destinate a telefoni e Tablet possa presupporre che tutte le unità di misura sono basate sul criterio seguente:

- 160 unità per pollice, equivalente a
- unità di 64 per il centimetro

Di sola lettura [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) e [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) le proprietà definite da `VisualElement` presentano predefinito "fittizio" valori di &ndash;1. Solo quando un elemento è stato ridimensionato e gestito nei layout di queste proprietà riflette le dimensioni effettive dell'elemento in unità indipendenti dal dispositivo. Questa dimensione include eventuali `Padding` impostato sull'elemento, ma non la `Margin`.

Viene generato un elemento visivo la [ `SizeChanged` ](xref:Xamarin.Forms.VisualElement.SizeChanged) evento quando relativo `Width` o `Height` è stato modificato. Il [ **WhatSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) esempio viene usato questo evento per visualizzare le dimensioni della schermata del programma.

## <a name="metrical-sizes"></a>Dimensioni di metrica:

Il [ **MetricalBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) Usa [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) per visualizzare un `BoxView` un pollice talla e l'altra larghezza centimetri.

## <a name="estimated-font-sizes"></a>Dimensioni dei caratteri stimato

Il [ **caratteri del testo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) esempio viene illustrato come usare la regola di 160 unità-a-the-pollice per specificare le dimensioni dei caratteri in unità di punti. La coerenza visiva tra le piattaforme che usano questa tecnica è migliore `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Adattamento del testo di dimensioni disponibili

È possibile adattare un blocco di testo all'interno di un rettangolo particolare calcolando una `FontSize` del `Label` usando i criteri seguenti:

- L'interlinea è 120% delle dimensioni del carattere (130% nelle piattaforme Windows).
- Media di un carattere width è 50% delle dimensioni del carattere.

Il [ **EstimatedFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) illustra questa tecnica. Questo programma è stato scritto prima la [ `Margin` ](xref:Xamarin.Forms.View.Margin) proprietà era disponibile, pertanto vengono usati un [ `ContentView` ](xref:Xamarin.Forms.ContentView) con un [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) impostazione per simulare un margine.

[![Schermata di triplo delle dimensioni del carattere stimato](images/ch05fg07-small.png "testo adatta alle dimensioni disponibili")](images/ch05fg07-large.png#lightbox "testo adatta alle dimensioni disponibili")

## <a name="a-fit-to-size-clock"></a>Un orologio fit-a-dimensioni

Il [ **FitToSizeClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) esempio viene illustrato l'utilizzo [ `Device.StartTimer` ](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) per avviare un timer che notifica periodicamente all'applicazione quando è necessario aggiornare l'orologio. Le dimensioni del carattere sono impostata su un-sesto della larghezza della pagina per rendere lo schermo di dimensioni corrispondenti.

## <a name="accessibility-issues"></a>Problemi di accessibilità

Il programma **EstimatedFontSize** e il programma **FitToSizeClock** contengono entrambi un lieve difetto: Se l'utente modifica le impostazioni di accessibilità del telefono in Android o Windows 10 Mobile, il programma non è più in grado di stimare il rendering del testo in base alle dimensioni del carattere. Il [ **AccessibilityTest** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) esempio viene illustrato il problema.

## <a name="empirically-fitting-text"></a>In modo empirico adattamento del testo

Un altro modo per adattare il testo a un rettangolo è in modo empirico calcolare le dimensioni del testo sottoposto a rendering e modificarlo verso l'alto o verso il basso. Il programma nelle chiamate libro [ `GetSizeRequest` ](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) su un elemento visivo per ottenere le dimensioni dell'elemento desiderato. Metodo obsoleto che programmi è necessario chiamare invece [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)).

Per un `Label`, il primo argomento deve essere la larghezza del contenitore (per consentire di ritorno a capo), mentre il secondo argomento deve essere impostato per `Double.PositiveInfinity` per rendere l'altezza non vincolato. Il [ **EmpiricalFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) illustra questa tecnica.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 5 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Esempi di capitolo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Capitolo 5 F# esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
