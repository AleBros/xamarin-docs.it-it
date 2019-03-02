---
title: Funzionalità della piattaforma iOS
description: Aggiunta di funzionalità specifiche di iOS per le applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2018
---

# <a name="ios-platform-features"></a>Funzionalità della piattaforma iOS

Sviluppo di applicazioni xamarin. Forms per iOS richiede Visual Studio. Il [pagina dei requisiti](~/get-started/requirements.md) contiene altre informazioni sui prerequisiti.

## <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti.

Le funzionalità specifiche della piattaforma seguenti viene fornita per xamarin. Forms visualizzazioni pagine e i layout in iOS:

- Offusca il supporto per qualunque [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [sfocatura VisualElement in iOS](visualelement-blur.md).
- Disattivazione della modalità colore legacy in supportato [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [modalità di colore VisualElement Legacy in iOS](legacy-color-mode.md).
- Abilitare un'ombreggiatura in una [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [VisualElement ombreggiature in iOS](visualelement-drop-shadow.md).

Le funzionalità specifiche della piattaforma seguenti viene fornita per le visualizzazioni di xamarin. Forms in iOS:

- Garantire che il testo di input si inserisce in un' [ `Entry` ](xref:Xamarin.Forms.Entry) modificando le dimensioni del carattere. Per altre informazioni, vedere [voce Font Size in iOS](entry-font-size.md).
- Impostare il colore del cursore un [ `Entry` ](xref:Xamarin.Forms.Entry). Per altre informazioni, vedere [colore cursore di movimento in iOS](entry-cursor-color.md).
- Impostando lo stile dei separatori in un [ `ListView` ](xref:Xamarin.Forms.ListView). Per altre informazioni, vedere [stile dei separatori di ListView in iOS](listview-separator-style.md).
- Il controllo quando si verifica la selezione di elementi in un [ `Picker` ](xref:Xamarin.Forms.Picker). Per altre informazioni, vedere [selezione di elementi di selezione in iOS](picker-selection.md).
- L'abilitazione di [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) proprietà da impostare toccando in base a una posizione per il [ `Slider` ](xref:Xamarin.Forms.Slider) barra, anziché dover trascinare il `Slider` thumb. Per altre informazioni, vedere [dispositivo di scorrimento controllo Thumb toccare su iOS](slider-thumb.md).

Le funzionalità specifiche della piattaforma seguenti viene fornita per le pagine di xamarin. Forms in iOS:

- Se si nasconde il separatore barra di navigazione in una [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Per altre informazioni, vedere [NavigationPage Bar Separator in iOS](navigation-bar-separator.md).
- Controllare se la barra di navigazione è semitrasparente. Per altre informazioni, vedere [traslucidità barra di navigazione in iOS](navigation-bar-translucent.md).
- Controllare se il testo della barra di stato di colore in una [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) viene regolata in modo da corrispondere la luminosità della barra di spostamento. Per altre informazioni, vedere [modalità di colore testo barra NavigationPage in iOS](status-bar-text-color.md).
- Controllare se il titolo della pagina viene visualizzato come un titolo di grandi dimensioni nella barra di spostamento della pagina. Per altre informazioni, vedere [titoli di pagina di grandi dimensioni in iOS](page-large-title.md).
- Impostazione di visibilità della barra di stato in una [ `Page` ](xref:Xamarin.Forms.Page). Per altre informazioni, vedere [visibilità della barra di stato pagina in iOS](page-status-bar-visibility.md).
- Garantire tale contenuto della pagina è posizionato su un'area dello schermo sicuro per tutti i dispositivi iOS. Per altre informazioni, vedere [Guida Layout dell'Area sicura in iOS](page-safe-area-layout.md).
- Impostazione dello stile di presentazione delle pagine modali in un iPad. Per altre informazioni, vedere [iPad modale stile di visualizzazione pagina presentazione](ipad-page-presentation-style.md).

Le funzionalità specifiche della piattaforma seguenti viene fornita per il layout di xamarin. Forms in iOS:

- Controllare se un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) gestisce un gesto tocco o passarlo al relativo contenuto. Per altre informazioni, vedere [ScrollView riguarda contenuto iOS](scrollview-content-touches.md).

Le funzionalità specifiche della piattaforma seguenti viene fornita per xamarin. Forms [ `Application` ](xref:Xamarin.Forms.Application) classe in iOS:

- L'abilitazione di layout del controllo e gli aggiornamenti da eseguire sul thread principale di rendering. Per altre informazioni, vedere [Main Thread controllare gli aggiornamenti in iOS](main-thread-updates-ui.md).
- Abilitazione di un [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) in una visualizzazione a scorrimento per acquisire e condividere il movimento di zoom con la visualizzazione a scorrimento. Per altre informazioni, vedere [simultanee riconoscimento del movimento di zoom in iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>formattazione specifica iOS

Xamarin. Forms consente gli stili dell'interfaccia utente di multi-piattaforma e i colori da impostare, ma sono disponibili altre opzioni per impostare il tema della finestra di iOS usando le API della piattaforma nel progetto iOS.

[Leggere altre](formatting.md) sulla formattazione l'interfaccia utente usando le API iOS specifici, ad esempio **Info. plist** configuration e `UIAppearance` API.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Altre funzionalità di iOS

Usando [renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), il [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)e il [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), è possibile incorporare un'ampia gamma di funzionalità native in Applicazioni xamarin. Forms per iOS.
