---
title: Riepilogo del capitolo 5. Gestione delle dimensioni
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 71d40136d7c3fc780815471cb822e94a4fa704c5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Riepilogo del capitolo 5. Gestione delle dimensioni

Dimensioni diverse in xamarin. Forms sono state rilevate fino a questo punto:

- L'altezza della barra di stato iOS è 20
- Il `BoxView` ha una larghezza predefinita e l'altezza di 40
- Il valore predefinito `Padding` in un `Frame` è 20
- Il valore predefinito `Spacing` sul `StackLayout` è 6.
- Il `Device.GetNamedSize` metodo restituisce una dimensione del carattere numerico

Queste dimensioni non sono i pixel. Sono invece unità indipendenti dal dispositivo riconosciuta in modo indipendente per ogni piattaforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixel, punti, dei punti di distribuzione, DIP e DIUs

Nelle prime fasi cronologie di Apple Mac e Microsoft Windows, i programmatori funzionavano in unità di pixel. Tuttavia, l'avvento di visualizzazioni con risoluzione superiore è necessario un approccio più astratto e virtualizzato in coordinate dello schermo. Nel mondo Mac, i programmatori funzionavano in unità di *punti*, in genere 1/72 pollice, mentre gli sviluppatori di Windows utilizzato *unità indipendenti dal dispositivo* (DIUs) in base 1/96 di pollice.

I dispositivi mobili, tuttavia, in genere vengono mantenuti molto più simile per il tipo di carattere e avere una risoluzione più alta rispetto a desktop schermate, che implica che è possibile tollerare una maggiore densità in pixel.

I programmatori destinate a dispositivi di Apple iPhone e iPad continuano a funzionare in unità di *punti*, ma esistono 160 questi punti per pollice. A seconda del dispositivo, potrebbe essere 1, 2 o 3 pixel per il punto.

Android è simile. I programmatori di lavoro in unità di *independent densità di pixel* (dp), e la relazione tra dei punti di distribuzione e pixel si basa su un 160 DP al pollice.

Windows Runtime ha stabilito anche i fattori di scala che implicano qualcosa di simile a 160 unità indipendenti dal dispositivo di pollice.

In breve, un programmatore di xamarin. Forms destinato a telefoni e Tablet può presupporre che tutte le unità di misura sono basate sul criterio seguente:

- 160 unità per pollice, equivalente a
- 64 unità per il centimetro

La proprietà di sola lettura [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) e [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) proprietà definite da `VisualElement` avere predefinito "simulare" valori di &ndash;1. Solo quando un elemento è stato ridimensionato e gestito nei layout di queste proprietà riflette le dimensioni effettive dell'elemento in unità indipendenti dal dispositivo. Questa dimensione include qualsiasi `Padding` impostato sull'elemento, ma non il `Margin`.

Viene generato un elemento visivo il [ `SizeChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.SizeChanged/) eventi quando il relativo `Width` o `Height` è stato modificato. Il [ **WhatSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) viene utilizzato questo evento per visualizzare le dimensioni dello schermo del programma.

## <a name="metrical-sizes"></a>Dimensioni di metrica:

Il [ **MetricalBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) Usa [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) e [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) per visualizzare un `BoxView` un pollice altezza e una centimetro wide.

## <a name="estimated-font-sizes"></a>Dimensioni stimate

Il [ **caratteri del testo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) esempio viene illustrato come utilizzare la regola di 160 unità-in-the-pollici per specificare le dimensioni dei caratteri in unità di punti. La coerenza tra le piattaforme utilizzando questa tecnica visual è migliore `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Adattamento del testo di dimensioni disponibili

È possibile adattare un blocco di testo all'interno di un rettangolo particolare calcolando un `FontSize` del `Label` utilizzando i criteri seguenti:

- Interlinea è 120% delle dimensioni del carattere (130% nelle piattaforme Windows).
- Larghezza del carattere medio è il 50% delle dimensioni del carattere.

Il [ **EstimatedFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) illustra questa tecnica. Questo programma è stato scritto prima il [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) proprietà è disponibile, quindi viene utilizzato un [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) con un [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) impostazione per simulare un margine.

[![Schermata di triplo della dimensione del carattere stimato](images/ch05fg07-small.png "testo adatta alle dimensioni disponibili")](images/ch05fg07-large.png#lightbox "testo adatta alle dimensioni disponibili")

## <a name="a-fit-to-size-clock"></a>Un clock adatta per dimensioni

Il [ **FitToSizeClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) esempio viene illustrato come utilizzare [ `Device.StartTimer` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.StartTimer/p/System.TimeSpan/System.Func%7BSystem.Boolean%7D/) per avviare un timer notifica periodicamente l'applicazione quando è necessario aggiornare l'orologio. Le dimensioni del carattere è un-sesto della larghezza della pagina per rendere lo schermo più grande possibile.

## <a name="accessibility-issues"></a>Problemi di accesso facilitato

Il **EstimatedFontSize** programma e **FitToSizeClock** programma entrambe contengono un difetto meno evidente: se l'utente modifica le impostazioni di accessibilità del telefono Android o Windows 10 Mobile, il programma non è più per stimare il testo viene visualizzato in base alla dimensione del carattere. Il [ **AccessibilityTest** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) esempio viene illustrato il problema.

## <a name="empirically-fitting-text"></a>In modo empirico adattamento del testo

Un altro modo per adattare il testo a un rettangolo è ed empiricamente calcolare le dimensioni del testo visualizzabile e modificarlo verso l'alto o verso il basso. Il programma nelle chiamate libro [ `GetSizeRequest` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.GetSizeRequest/p/System.Double/System.Double/) su un elemento visivo per ottenere le dimensioni dell'elemento desiderato. Metodo è obsoleto che i programmi possono invece chiamare [ `Measure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/).

Per un `Label`, il primo argomento deve essere la larghezza del contenitore (per consentire di ritorno a capo), mentre il secondo argomento deve essere impostato a `Double.PositiveInfinity` affinché l'altezza non vincolato. Il [ **EmpiricalFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) illustra questa tecnica.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 5 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Esempi di capitolo 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Esempi di capitolo 5 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
