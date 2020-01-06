---
title: funzionalità della piattaforma iOS in Novell. Forms
description: Aggiunta di funzionalità specifiche di iOS per le applicazioni Novell. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 5d0e289ddeb7eabef6d96c8882c772c704c54b34
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489726"
---
# <a name="ios-platform-features-in-xamarinforms"></a>funzionalità della piattaforma iOS in Novell. Forms

Per lo sviluppo di applicazioni Novell. Forms per iOS è necessario Visual Studio. La [pagina requisiti](~/get-started/requirements.md) contiene altre informazioni sui prerequisiti.

## <a name="platform-specifics"></a>Specifiche della piattaforma

Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti.

Per le visualizzazioni, le pagine e i layout di Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Offusca il supporto per qualunque [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere la pagina [relativa alla sfocatura di visualElement in iOS](visualelement-blur.md).
- Disattivazione della modalità colore legacy in supportato [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [modalità colore legacy di visualElement in iOS](legacy-color-mode.md).
- Abilitare un'ombreggiatura in una [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per ulteriori informazioni, vedere la pagina relativa all'eliminazione di oggetti [visivi in iOS](visualelement-drop-shadow.md).

Per le visualizzazioni Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Impostazione del colore di sfondo [`Cell`](xref:Xamarin.Forms.Cell) . Per ulteriori informazioni, vedere la pagina [relativa al colore di sfondo della cella in iOS](cell-background-color.md).
- Garantire che il testo di input si inserisce in un' [ `Entry` ](xref:Xamarin.Forms.Entry) modificando le dimensioni del carattere. Per altre informazioni, vedere [entry font size in iOS](entry-font-size.md).
- Impostare il colore del cursore un [ `Entry` ](xref:Xamarin.Forms.Entry). Per altre informazioni, vedere [entry Cursor color on iOS](entry-cursor-color.md).
- Controllare se le celle di intestazione [`ListView`](xref:Xamarin.Forms.ListView) fluttuano durante lo scorrimento. Per altre informazioni, vedere [stile intestazione gruppo ListView in iOS](listview-group-header-style.md).
- Controllo della disabilitazione delle animazioni di riga durante l'aggiornamento della raccolta di elementi [`ListView`](xref:Xamarin.Forms.ListView) . Per altre informazioni, vedere [animazioni di riga ListView in iOS](listview-row-animations.md).
- Impostando lo stile dei separatori in un [ `ListView` ](xref:Xamarin.Forms.ListView). Per altre informazioni, vedere [stile separatore ListView in iOS](listview-separator-style.md).
- Il controllo quando si verifica la selezione di elementi in un [ `Picker` ](xref:Xamarin.Forms.Picker). Per altre informazioni, vedere [selezione di elementi selezione in iOS](picker-selection.md).
- L'abilitazione di [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) proprietà da impostare toccando in base a una posizione per il [ `Slider` ](xref:Xamarin.Forms.Slider) barra, anziché dover trascinare il `Slider` thumb. Per altre informazioni, vedere [dispositivo di scorrimento tocco in iOS](slider-thumb.md).
- Controllo della transizione utilizzata per l'apertura di un `SwipeView`. Per altre informazioni, vedere [modalità di transizione SwipeView swipe](swipeview-swipetransitionmode.md).

Per le pagine Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Se si nasconde il separatore barra di navigazione in una [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Per altre informazioni, vedere [separatore della barra NavigationPage in iOS](navigation-bar-separator.md).
- Controllare se la barra di navigazione è semitrasparente. Per altre informazioni, vedere [traslucidità della barra di spostamento in iOS](navigation-bar-translucent.md).
- Controllare se il testo della barra di stato di colore in una [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) viene regolata in modo da corrispondere la luminosità della barra di spostamento. Per altre informazioni, vedere la [modalità colore del testo della barra NavigationPage in iOS](status-bar-text-color.md).
- Controllare se il titolo della pagina viene visualizzato come un titolo di grandi dimensioni nella barra di spostamento della pagina. Per altre informazioni, vedere [titoli di pagine di grandi dimensioni in iOS](page-large-title.md).
- Impostazione della visibilità dell'indicatore Home in un [`Page`](xref:Xamarin.Forms.Page). Per ulteriori informazioni, vedere [visibilità dell'indicatore Home in iOS](page-home-indicator.md).
- Impostazione di visibilità della barra di stato in una [ `Page` ](xref:Xamarin.Forms.Page). Per ulteriori informazioni, vedere [visibilità della barra di stato della pagina in iOS](page-status-bar-visibility.md).
- Garantire tale contenuto della pagina è posizionato su un'area dello schermo sicuro per tutti i dispositivi iOS. Per altre informazioni, vedere [Guida al layout dell'area sicura in iOS](page-safe-area-layout.md).
- Impostazione dello stile di presentazione delle pagine modali. Per altre informazioni, vedere [stile di presentazione della pagina modale](page-presentation-style.md).

Per i layout di Novell. Forms in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Controllare se un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) gestisce un gesto tocco o passarlo al relativo contenuto. Per altre informazioni, vedere [ScrollView content touchs on iOS](scrollview-content-touches.md).

Per la classe Novell. Forms [`Application`](xref:Xamarin.Forms.Application) in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Disabilitazione del ridimensionamento dell'accessibilità per le dimensioni del carattere denominato. Per altre informazioni, vedere [ridimensionamento dell'accessibilità per le dimensioni del carattere denominate in iOS](named-font-size-scaling.md).
- L'abilitazione di layout del controllo e gli aggiornamenti da eseguire sul thread principale di rendering. Per ulteriori informazioni, vedere la pagina [relativa agli aggiornamenti principali del controllo thread in iOS](main-thread-updates-ui.md).
- Abilitazione di un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) in una visualizzazione a scorrimento per acquisire e condividere il movimento di zoom con la visualizzazione a scorrimento. Per altre informazioni, vedere [il riconoscimento dei movimenti di Pan simultanei in iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>formattazione specifica di iOS

Novell. Forms consente di impostare stili e colori dell'interfaccia utente multipiattaforma, ma sono disponibili altre opzioni per l'impostazione del tema di iOS usando le API della piattaforma nel progetto iOS.

[Altre](formatting.md) informazioni sulla formattazione dell'interfaccia utente usando le API specifiche di iOS, ad esempio la configurazione di **info. plist** e l'API `UIAppearance`.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Altre funzionalità iOS

Utilizzando [renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)e [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), è possibile incorporare un'ampia gamma di funzionalità native nelle applicazioni Novell. Forms per iOS.
