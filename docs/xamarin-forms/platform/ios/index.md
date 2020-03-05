---
title: funzionalità della piattaforma iOS in Novell. Forms
description: Aggiunta di funzionalità specifiche di iOS per le applicazioni Novell. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: 015db40ce983d979109d4cce32c011f8c61a729c
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291849"
---
# <a name="ios-platform-features-in-xamarinforms"></a>funzionalità della piattaforma iOS in Novell. Forms

Per lo sviluppo di applicazioni Novell. Forms per iOS è necessario Visual Studio. La [pagina piattaforme supportate](~/get-started/supported-platforms.md) contiene altre informazioni sui prerequisiti.

## <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti.

Per le visualizzazioni, le pagine e i layout di Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Supporto di sfocatura per qualsiasi [`VisualElement`](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere la pagina [relativa alla sfocatura di visualElement in iOS](visualelement-blur.md).
- Disabilitazione della modalità colore legacy in una [`VisualElement`](xref:Xamarin.Forms.VisualElement)supportata. Per altre informazioni, vedere [modalità colore legacy di visualElement in iOS](legacy-color-mode.md).
- Abilitazione di un'ombreggiatura in un [`VisualElement`](xref:Xamarin.Forms.VisualElement). Per ulteriori informazioni, vedere la pagina relativa all'eliminazione di oggetti [visivi in iOS](visualelement-drop-shadow.md).
- Consentire a un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) di diventare il primo risponditore per gli eventi di tocco. Per altre informazioni, vedere [primo risponditore di visualElement](visualelement-first-responder.md).

Per le visualizzazioni Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Impostazione del colore di sfondo [`Cell`](xref:Xamarin.Forms.Cell) . Per ulteriori informazioni, vedere la pagina [relativa al colore di sfondo della cella in iOS](cell-background-color.md).
- Controllo quando la selezione dell'elemento viene eseguita in un [`DatePicker`](xref:Xamarin.Forms.DatePicker). Per altre informazioni, vedere [selezione di elementi DatePicker in iOS](datepicker-selection.md).
- Assicurandosi che il testo inserito si trovi in un [`Entry`](xref:Xamarin.Forms.Entry) regolando le dimensioni del carattere. Per altre informazioni, vedere [entry font size in iOS](entry-font-size.md).
- Impostazione del colore del cursore in un [`Entry`](xref:Xamarin.Forms.Entry). Per altre informazioni, vedere [entry Cursor color on iOS](entry-cursor-color.md).
- Controllare se le celle di intestazione [`ListView`](xref:Xamarin.Forms.ListView) fluttuano durante lo scorrimento. Per altre informazioni, vedere [stile intestazione gruppo ListView in iOS](listview-group-header-style.md).
- Controllo della disabilitazione delle animazioni di riga durante l'aggiornamento della raccolta di elementi [`ListView`](xref:Xamarin.Forms.ListView) . Per altre informazioni, vedere [animazioni di riga ListView in iOS](listview-row-animations.md).
- Impostazione dello stile del separatore su un [`ListView`](xref:Xamarin.Forms.ListView). Per altre informazioni, vedere [stile separatore ListView in iOS](listview-separator-style.md).
- Controllo quando la selezione dell'elemento viene eseguita in un [`Picker`](xref:Xamarin.Forms.Picker). Per altre informazioni, vedere [selezione di elementi selezione in iOS](picker-selection.md).
- Per impostare la proprietà [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) è necessario toccare una posizione sulla barra [`Slider`](xref:Xamarin.Forms.Slider) , anziché trascinare il cursore del `Slider`. Per altre informazioni, vedere [dispositivo di scorrimento tocco in iOS](slider-thumb.md).
- Controllo della transizione utilizzata per l'apertura di un `SwipeView`. Per altre informazioni, vedere [modalità di transizione SwipeView swipe](swipeview-swipetransitionmode.md).
- Controllo quando la selezione dell'elemento viene eseguita in un [`TimePicker`](xref:Xamarin.Forms.TimePicker). Per altre informazioni, vedere [selezione di elementi TimePicker in iOS](timepicker-selection.md).

Per le pagine Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Nascondere il separatore della barra di navigazione in un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Per altre informazioni, vedere [separatore della barra NavigationPage in iOS](navigation-bar-separator.md).
- Controllare se la barra di navigazione è semitrasparente. Per altre informazioni, vedere [traslucidità della barra di spostamento in iOS](navigation-bar-translucent.md).
- Controllare se il colore del testo della barra di stato in un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) viene regolato in modo da corrispondere alla luminosità della barra di spostamento. Per altre informazioni, vedere la [modalità colore del testo della barra NavigationPage in iOS](status-bar-text-color.md).
- Controllare se il titolo della pagina viene visualizzato come un titolo di grandi dimensioni nella barra di spostamento della pagina. Per altre informazioni, vedere [titoli di pagine di grandi dimensioni in iOS](page-large-title.md).
- Impostazione della visibilità dell'indicatore Home in un [`Page`](xref:Xamarin.Forms.Page). Per ulteriori informazioni, vedere [visibilità dell'indicatore Home in iOS](page-home-indicator.md).
- Impostazione della visibilità della barra di stato su un [`Page`](xref:Xamarin.Forms.Page). Per ulteriori informazioni, vedere [visibilità della barra di stato della pagina in iOS](page-status-bar-visibility.md).
- Garantire tale contenuto della pagina è posizionato su un'area dello schermo sicuro per tutti i dispositivi iOS. Per altre informazioni, vedere [Guida al layout dell'area sicura in iOS](page-safe-area-layout.md).
- Impostazione dello stile di presentazione delle pagine modali. Per altre informazioni, vedere [stile di presentazione della pagina modale](page-presentation-style.md).
- Impostazione della modalità di traslucidità della barra delle schede in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Per altre informazioni, vedere [TabbedPage translucidar in iOS](tabbedpage-translucent-tabbar.md).

Per i layout di Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Controllare se un [`ScrollView`](xref:Xamarin.Forms.ScrollView) gestisce un movimento tocco o lo passa al relativo contenuto. Per altre informazioni, vedere [ScrollView content touchs on iOS](scrollview-content-touches.md).

Per la classe Novell. Forms [`Application`](xref:Xamarin.Forms.Application) in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Disabilitazione del ridimensionamento dell'accessibilità per le dimensioni del carattere denominato. Per altre informazioni, vedere [ridimensionamento dell'accessibilità per le dimensioni del carattere denominate in iOS](named-font-size-scaling.md).
- L'abilitazione di layout del controllo e gli aggiornamenti da eseguire sul thread principale di rendering. Per ulteriori informazioni, vedere la pagina [relativa agli aggiornamenti principali del controllo thread in iOS](main-thread-updates-ui.md).
- Abilitazione di un [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) in una visualizzazione a scorrimento per acquisire e condividere il movimento della panoramica con la visualizzazione a scorrimento. Per altre informazioni, vedere [il riconoscimento dei movimenti di Pan simultanei in iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>formattazione specifica di iOS

Novell. Forms consente di impostare stili e colori dell'interfaccia utente multipiattaforma, ma sono disponibili altre opzioni per l'impostazione del tema di iOS usando le API della piattaforma nel progetto iOS.

[Altre](formatting.md) informazioni sulla formattazione dell'interfaccia utente usando le API specifiche di iOS, ad esempio la configurazione di **info. plist** e l'API `UIAppearance`.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Altre funzionalità iOS

Utilizzando [renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)e [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), è possibile incorporare un'ampia gamma di funzionalità native nelle applicazioni Novell. Forms per iOS.
