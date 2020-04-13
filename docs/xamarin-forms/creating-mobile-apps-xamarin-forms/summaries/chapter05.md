---
title: Riassunto del capitolo 5. Gestione delle dimensioni
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 5. Gestione delle dimensioni'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771147"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Riassunto del capitolo 5. Gestione delle dimensioni

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui Xamarin.Forms si è discostata dal materiale presentato nel libro.

Finora sono state riscontrate diverse taglie in Xamarin.Forms:

- L'altezza della barra di stato di iOS è 20
- Il `BoxView` ha una larghezza e un'altezza predefinite di 40
- Il `Padding` valore `Frame` predefinito in a è 20
- L'impostazione predefinita `Spacing` nella `StackLayout`
- Il `Device.GetNamedSize` metodo restituisce una dimensione numerica del carattere

Queste dimensioni non sono pixel. Sono invece unità indipendenti dal dispositivo riconosciute in modo indipendente da ogni piattaforma.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixel, punti, dps, DIP e DIUs

All'inizio delle storie di Apple Mac e Microsoft Windows, i programmatori hanno lavorato in unità di pixel. Tuttavia, l'avvento dei display ad alta risoluzione richiedeva un approccio più virtualizzato e astratto alle coordinate dello schermo. Nel mondo Mac, i programmatori lavoravano in unità di *punti,* tradizionalmente 1/72 pollici, mentre gli sviluppatori Windows usavano *unità indipendenti dal dispositivo* (DIUs) basate su 1/96 di pollice.

I dispositivi mobili, tuttavia, sono generalmente tenuti molto più vicini al viso e hanno una risoluzione più alta rispetto agli schermi desktop, il che implica che una maggiore densità di pixel può essere tollerata.

I programmatori destinati ai dispositivi Apple iPhone e iPad continuano a lavorare in unità di *punti,* ma ci sono 160 di questi punti al pollice. A seconda del dispositivo, potrebbero essere presenti 1, 2 o 3 pixel al punto.

Android è simile. I programmatori lavorano in unità di *pixel indipendenti dalla densità* (dps) e la relazione tra dps e pixel si basa su 160 dps al pollice.

I telefoni Windows e i dispositivi mobili hanno anche stabilito fattori di scala che implicano qualcosa di vicino a 160 unità indipendenti dal dispositivo al pollice.

> [!NOTE]
> Xamarin.Forms non supporta più alcun telefono o dispositivo mobile basato su Windows.

In sintesi, un programmatore Xamarin.Forms destinato telefoni e tablet può presumere che tutte le unità di misura si basino sul seguente criterio:

- 160 unità al pollice, equivalente a
- 64 unità al centimetro

Le proprietà [`Width`](xref:Xamarin.Forms.VisualElement.Width) di [`Height`](xref:Xamarin.Forms.VisualElement.Height) sola `VisualElement` lettura e definite da &ndash;hanno valori "mock" predefiniti pari a 1.The read-only and properties defined by have default "mock" values of 1. Solo quando un elemento è stato ridimensionato e adattato nel layout, queste proprietà rifletteranno le dimensioni effettive dell'elemento in unità indipendenti dal dispositivo. Questa dimensione `Padding` include qualsiasi set sull'elemento ma non il `Margin`file .

Un elemento visivo [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) genera `Width` l'evento quando è stato modificato o `Height` è stato modificato. Nell'esempio [**WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) viene utilizzato questo evento per visualizzare le dimensioni dello schermo del programma.

## <a name="metrical-sizes"></a>Dimensioni metriche

Il [**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) utilizza [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) per visualizzare un `BoxView` pollice di larghezza e un centimetro di larghezza.

## <a name="estimated-font-sizes"></a>Dimensioni dei caratteri stimate

Nell'esempio [**FontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) viene illustrato come utilizzare la regola da 160 unità a pollice per specificare le dimensioni dei caratteri in unità di punti. La coerenza visiva tra le piattaforme `Device.GetNamedSize`che utilizzano questa tecnica è migliore di .

## <a name="fitting-text-to-available-size"></a>Adattamento del testo alle dimensioni disponibili

È possibile adattare un blocco di testo all'interno `FontSize` di `Label` un particolare rettangolo calcolando uno dei seguenti criteri:

- L'interlinea è pari al 120% della dimensione del carattere (130% sulle piattaforme Windows).
- La larghezza media dei caratteri è pari al 50% della dimensione del carattere.

Nell'esempio [**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) viene illustrata questa tecnica. Questo programma è [`Margin`](xref:Xamarin.Forms.View.Margin) stato scritto prima che [`ContentView`](xref:Xamarin.Forms.ContentView) la [`Padding`](xref:Xamarin.Forms.Layout.Padding) proprietà fosse disponibile, quindi utilizza un con un'impostazione per simulare un margine.

[![Tripla schermata della dimensione stimata del carattere](images/ch05fg07-small.png "Adatta testo alle dimensioni disponibili")](images/ch05fg07-large.png#lightbox "Adatta testo alle dimensioni disponibili")

## <a name="a-fit-to-size-clock"></a>Un orologio adatto alle dimensioni

[**Nell'esempio FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) viene illustrato l'utilizzo [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) per avviare un timer che notifica periodicamente all'applicazione quando è il momento di aggiornare l'orologio. La dimensione del carattere è impostata su un sesto della larghezza della pagina per rendere la visualizzazione il più grande possibile.

## <a name="accessibility-issues"></a>Problemi di accessibilità

Il programma **EstimatedFontSize** e il programma **FitToSizeClock** contengono entrambi un difetto sottile: se l'utente modifica le impostazioni di accessibilità del telefono su Android o Windows 10 Mobile, il programma non può più stimare le dimensioni del testo viene sottoposto a rendering in base alla dimensione del carattere. [**Nell'esempio AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) viene illustrato questo problema.

## <a name="empirically-fitting-text"></a>Testo adattato empiricamente

Un altro modo per adattare il testo a un rettangolo consiste nel calcolare empiricamente la dimensione del testo sottoposto a rendering e regolarla verso l'alto o verso il basso. Il programma nel [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) libro chiama un elemento visivo per ottenere la dimensione desiderata dell'elemento. Tale metodo è stato deprecato e [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))i programmi devono invece chiamare .

Per `Label`un oggetto , il primo argomento deve essere la larghezza del contenitore `Double.PositiveInfinity` (per consentire il wrapping), mentre il secondo argomento deve essere impostato su per rendere l'altezza non vincolata. Nell'esempio [**EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) viene illustrata questa tecnica.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 5 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Capitolo 5 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Capitolo 5 Esempi di F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
