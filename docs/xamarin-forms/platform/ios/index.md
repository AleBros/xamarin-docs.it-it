---
title: funzionalità della piattaforma iOS in Novell. Forms
description: Aggiunta di funzionalità specifiche di iOS per le applicazioni Novell. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
ms.openlocfilehash: 97b9b70a1df61beb7b064c99721b4277e2570b41
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517051"
---
# <a name="ios-platform-features-in-xamarinforms"></a>funzionalità della piattaforma iOS in Novell. Forms

Per lo sviluppo di applicazioni Novell. Forms per iOS è necessario Visual Studio. La [pagina piattaforme supportate](~/get-started/supported-platforms.md) contiene altre informazioni sui prerequisiti.

## <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati.

Per le visualizzazioni, le pagine e i layout di Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Supporto di sfocatura [`VisualElement`](xref:Xamarin.Forms.VisualElement)per qualsiasi. Per altre informazioni, vedere la pagina [relativa alla sfocatura di visualElement in iOS](visualelement-blur.md).
- Disabilitazione della modalità colore legacy su un [`VisualElement`](xref:Xamarin.Forms.VisualElement)supportato. Per altre informazioni, vedere [modalità colore legacy di visualElement in iOS](legacy-color-mode.md).
- Abilitazione di un'ombreggiatura [`VisualElement`](xref:Xamarin.Forms.VisualElement)in un oggetto. Per ulteriori informazioni, vedere la pagina relativa all'eliminazione di oggetti [visivi in iOS](visualelement-drop-shadow.md).
- Consentire a [`VisualElement`](xref:Xamarin.Forms.VisualElement) un oggetto di diventare il primo risponditore per il tocco degli eventi. Per altre informazioni, vedere [primo risponditore di visualElement](visualelement-first-responder.md).

Per le visualizzazioni Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Impostazione del [`Cell`](xref:Xamarin.Forms.Cell) colore di sfondo. Per ulteriori informazioni, vedere la pagina [relativa al colore di sfondo della cella in iOS](cell-background-color.md).
- Controllo quando viene eseguita la selezione dell' [`DatePicker`](xref:Xamarin.Forms.DatePicker)elemento in un oggetto. Per altre informazioni, vedere [selezione di elementi DatePicker in iOS](datepicker-selection.md).
- Assicurandosi che il testo inserito venga inserito [`Entry`](xref:Xamarin.Forms.Entry) in un regolando le dimensioni del carattere. Per altre informazioni, vedere [entry font size in iOS](entry-font-size.md).
- Impostazione del colore del cursore in [`Entry`](xref:Xamarin.Forms.Entry)un oggetto. Per altre informazioni, vedere [entry Cursor color on iOS](entry-cursor-color.md).
- Controllare se [`ListView`](xref:Xamarin.Forms.ListView) le celle di intestazione fluttuano durante lo scorrimento. Per altre informazioni, vedere [stile intestazione gruppo ListView in iOS](listview-group-header-style.md).
- Controllo della disabilitazione delle animazioni di riga [`ListView`](xref:Xamarin.Forms.ListView) durante l'aggiornamento della raccolta di elementi. Per altre informazioni, vedere [animazioni di riga ListView in iOS](listview-row-animations.md).
- Impostazione dello stile del separatore [`ListView`](xref:Xamarin.Forms.ListView)su. Per altre informazioni, vedere [stile separatore ListView in iOS](listview-separator-style.md).
- Controllo quando viene eseguita la selezione dell' [`Picker`](xref:Xamarin.Forms.Picker)elemento in un oggetto. Per altre informazioni, vedere [selezione di elementi selezione in iOS](picker-selection.md).
- Controllo della presenza [`SearchBar`](xref:Xamarin.Forms.SearchBar) di uno sfondo. Per altre informazioni, vedere [stile di Searchbar in iOS](searchbar-style.md).
- Abilitazione [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) della proprietà da impostare toccando una posizione sulla [`Slider`](xref:Xamarin.Forms.Slider) barra, anziché trascinare il `Slider` cursore. Per altre informazioni, vedere [dispositivo di scorrimento tocco in iOS](slider-thumb.md).
- Controllo della transizione utilizzata durante l'apertura di un `SwipeView`oggetto. Per altre informazioni, vedere [modalità di transizione SwipeView swipe](swipeview-swipetransitionmode.md).
- Controllo quando viene eseguita la selezione dell' [`TimePicker`](xref:Xamarin.Forms.TimePicker)elemento in un oggetto. Per altre informazioni, vedere [selezione di elementi TimePicker in iOS](timepicker-selection.md).

Per le pagine Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Controllo della presenza della pagina dei dettagli [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) di un oggetto a cui è applicata l'ombreggiatura quando viene rivelata la pagina master. Per ulteriori informazioni, vedere [MasterDetailPage Shadow](masterdetailpage-shadow.md).
- Nascondere il separatore della barra di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)navigazione in un oggetto. Per altre informazioni, vedere [separatore della barra NavigationPage in iOS](navigation-bar-separator.md).
- Controllo dell'eventuale traslucidità della barra di navigazione. Per altre informazioni, vedere [traslucidità della barra di spostamento in iOS](navigation-bar-translucent.md).
- Controllare se il colore del testo della barra di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) stato su un oggetto viene regolato in modo da corrispondere alla luminosità della barra di spostamento. Per altre informazioni, vedere la [modalità colore del testo della barra NavigationPage in iOS](status-bar-text-color.md).
- Controllare se il titolo della pagina viene visualizzato come titolo grande nella barra di spostamento della pagina. Per altre informazioni, vedere [titoli di pagine di grandi dimensioni in iOS](page-large-title.md).
- Impostazione della visibilità dell'indicatore Home in un oggetto [`Page`](xref:Xamarin.Forms.Page). Per ulteriori informazioni, vedere [visibilità dell'indicatore Home in iOS](page-home-indicator.md).
- Impostazione della visibilità della barra di stato [`Page`](xref:Xamarin.Forms.Page)su un oggetto. Per ulteriori informazioni, vedere [visibilità della barra di stato della pagina in iOS](page-status-bar-visibility.md).
- Verificare che il contenuto della pagina sia posizionato in un'area della schermata sicura per tutti i dispositivi iOS. Per altre informazioni, vedere [Guida al layout dell'area sicura in iOS](page-safe-area-layout.md).
- Impostazione dello stile di presentazione delle pagine modali. Per altre informazioni, vedere [stile di presentazione della pagina modale](page-presentation-style.md).
- Impostazione della modalità di traslucidità della barra delle schede [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)in un oggetto. Per altre informazioni, vedere [TabbedPage translucidar in iOS](tabbedpage-translucent-tabbar.md).

Per i layout di Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Controllare se un [`ScrollView`](xref:Xamarin.Forms.ScrollView) oggetto gestisce un movimento tocco o lo passa al relativo contenuto. Per altre informazioni, vedere [ScrollView content touchs on iOS](scrollview-content-touches.md).

Per la classe Novell. Forms [`Application`](xref:Xamarin.Forms.Application) in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Disabilitazione del ridimensionamento dell'accessibilità per le dimensioni del carattere denominato. Per altre informazioni, vedere [ridimensionamento dell'accessibilità per le dimensioni del carattere denominate in iOS](named-font-size-scaling.md).
- Abilitazione del layout e del rendering degli aggiornamenti del controllo da eseguire sul thread principale. Per ulteriori informazioni, vedere la pagina [relativa agli aggiornamenti principali del controllo thread in iOS](main-thread-updates-ui.md).
- Abilitazione [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) di un oggetto in una visualizzazione a scorrimento per acquisire e condividere il movimento della panoramica con la visualizzazione a scorrimento. Per altre informazioni, vedere [il riconoscimento dei movimenti di Pan simultanei in iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>formattazione specifica di iOS

Novell. Forms consente di impostare stili e colori dell'interfaccia utente multipiattaforma, ma sono disponibili altre opzioni per l'impostazione del tema di iOS usando le API della piattaforma nel progetto iOS.

[Altre](formatting.md) informazioni sulla formattazione dell'interfaccia utente usando le API specifiche di iOS, ad esempio la configurazione di **info. plist** e l' `UIAppearance` API.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Altre funzionalità iOS

Utilizzando [renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)e [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), è possibile incorporare un'ampia gamma di funzionalità native nelle applicazioni Novell. Forms per iOS.
