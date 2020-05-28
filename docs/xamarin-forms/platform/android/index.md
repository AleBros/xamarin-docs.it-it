---
title: ''
description: Questo articolo illustra come aggiungere funzionalità specifiche di Android alle Xamarin.Forms applicazioni.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3045db1248aa16529d4e43b9a8afc97377cfd9cb
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128951"
---
# <a name="android-platform-features"></a>Funzionalità della piattaforma Android

Per lo sviluppo Xamarin.Forms di applicazioni per Android è necessario Visual Studio. La [pagina piattaforme supportate](~/get-started/supported-platforms.md) contiene altre informazioni sui prerequisiti.

## <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati.

Per le Xamarin.Forms Visualizzazioni, le pagine e i layout in Android sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Controllo dell'ordine Z degli elementi visivi per determinare l'ordine di disegno. Per altre informazioni, vedere [elevazione dei privilegi visivi in Android](visualelement-elevation.md).
- Disabilitazione della modalità colore legacy su un supportato [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Per altre informazioni, vedere [modalità colore legacy di visualElement in Android](legacy-color-mode.md).

Per le visualizzazioni in Android sono disponibili le funzionalità specifiche della piattaforma seguenti Xamarin.Forms :

- Uso dei valori di riempimento e ombreggiatura predefiniti dei pulsanti Android. Per altre informazioni, vedere [riempimento dei pulsanti e ombre in Android](button-padding-shadow.md).
- Impostazione delle opzioni di Input Method Editor per la tastiera soft per un [`Entry`](xref:Xamarin.Forms.Entry) . Per altre informazioni, vedere [Opzioni dell'editor del metodo Entry input in Android](entry-ime-options.md).
- Abilitazione di un'ombreggiatura in un oggetto `ImageButton` . Per altre informazioni, vedere [ImageButton Drop Shadows in Android](imagebutton-drop-shadow.md).
- Per ulteriori informazioni sull'abilitazione dello scorrimento rapido in un oggetto [`ListView`](xref:Xamarin.Forms.ListView) , vedere la pagina [relativa allo scorrimento rapido di ListView in Android](listview-fast-scrolling.md).
- Controllo della transizione utilizzata durante l'apertura di un oggetto `SwipeView` . Per altre informazioni, vedere [modalità di transizione SwipeView swipe](swipeview-swipetransitionmode.md).
- Controllare se un oggetto [`WebView`](xref:Xamarin.Forms.WebView) può visualizzare contenuto misto. Per altre informazioni, vedere [WebView mixed content in Android](webview-mixed-content.md).
- Abilitazione dello zoom su un [`WebView`](xref:Xamarin.Forms.WebView) . Per altre informazioni, vedere [zoom di WebView in Android](webview-zoom-controls.md).

Per le celle in Android sono disponibili le funzionalità specifiche della piattaforma seguenti Xamarin.Forms :

- Abilitazione [`ViewCell`](xref:Xamarin.Forms.ViewCell) della modalità legacy delle azioni di contesto, in modo che il menu azioni del contesto non venga aggiornato quando l'elemento selezionato in viene [`ListView`](xref:Xamarin.Forms.ListView) modificato. Per altre informazioni, vedere [ViewCell context actions on Android](viewcell-context-actions.md).

Per le pagine in Android sono disponibili le funzionalità specifiche della piattaforma seguenti Xamarin.Forms :

- Impostazione dell'altezza della barra di spostamento su un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Per altre informazioni, vedere [altezza della barra NavigationPage in Android](navigationpage-bar-height.md).
- Disabilitazione delle animazioni di transizione durante l'esplorazione delle pagine in un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Per altre informazioni, vedere [TabbedPage page Transition animations on Android](tabbedpage-transition-animations.md).
- Abilitazione del swiping tra le pagine in un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Per altre informazioni, vedere [TabbedPage page swiping on Android](tabbedpage-page-swiping.md).
- Impostazione della posizione e del colore della barra degli strumenti in un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Per altre informazioni, vedere [selezione host e colore della barra degli strumenti TabbedPage in Android](tabbedpage-toolbar-placement-color.md).

Per la Xamarin.Forms classe in Android sono disponibili le funzionalità specifiche della piattaforma seguenti [`Application`](xref:Xamarin.Forms.Application) :

- Impostazione della modalità operativa di una tastiera soft. Per altre informazioni, vedere [modalità di input tastiera soft in Android](soft-keyboard-input-mode.md).
- Disabilitare gli eventi del ciclo di vita della [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) [`Appearing`](xref:Xamarin.Forms.Page.Appearing) pagina e in pausa e riprendere rispettivamente per le applicazioni che usano AppCompat. Per altre informazioni, vedere [eventi del ciclo di vita della pagina in Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Piattaforme supportate

Originariamente, il Xamarin.Forms progetto Android predefinito usava uno stile precedente di rendering del controllo che era comune prima di android 5,0. Le applicazioni compilate con il modello hanno `FormsApplicationActivity` come classe base dell'attività principale.

## <a name="material-design-via-appcompat"></a>Progettazione materiale tramite AppCompat

Xamarin.FormsI progetti Android ora usano `FormsAppCompatActivity` come classe di base dell'attività principale. Questa classe usa le funzionalità di **AppCompat** fornite da Android per implementare i temi di progettazione del materiale.

Per aggiungere temi di progettazione del materiale al Xamarin.Forms progetto Android, seguire le [istruzioni di installazione per il supporto di AppCompat](appcompat-material-design.md)

Di seguito è riportato l'esempio **todo** con l'impostazione predefinita `FormsApplicationActivity` :

[![](images/before-appcompat-sml.png "Todo Sample Application Without AppCompat")](images/before-appcompat.png#lightbox "Todo Sample Application Without AppCompat")

Si tratta dello stesso codice dopo l'aggiornamento del progetto da usare `FormsAppCompatActivity` (e l'aggiunta delle informazioni aggiuntive sul tema):

[![](images/post-appcompat-sml.png "Todo Sample Application With AppCompat and Theming")](images/post-appcompat.png#lightbox "Todo Sample Application With AppCompat and Theming")

> [!NOTE]
> Quando `FormsAppCompatActivity` si usa, le [classi di base per alcuni renderer personalizzati Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) saranno diverse.

## <a name="androidx-migration"></a>Migrazione AndroidX

AndroidX sostituisce la libreria di supporto Android. Per informazioni su AndroidX e su come eseguire la migrazione Xamarin.Forms di un'app per usare le librerie AndroidX, vedere [migrazione di AndroidX in Xamarin.Forms ](~/xamarin-forms/platform/android/androidx-migration.md).

## <a name="related-links"></a>Collegamenti correlati

- [Aggiunta del supporto per la progettazione di materiali](appcompat-material-design.md)
