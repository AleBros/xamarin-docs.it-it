---
title: Funzionalità della piattaforma Android
description: Questo articolo illustra come aggiungere funzionalità specifiche di Android alle applicazioni Novell. Forms.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 7ad7349c89913129cccdd77ac843188cbe668571
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291647"
---
# <a name="android-platform-features"></a>Funzionalità della piattaforma Android

Per lo sviluppo di applicazioni Novell. Forms per Android è necessario Visual Studio. La [pagina piattaforme supportate](~/get-started/supported-platforms.md) contiene altre informazioni sui prerequisiti.

## <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti.

Per le visualizzazioni, le pagine e i layout di Novell. Forms in Android sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Controllare l'ordine Z degli elementi visivi per determinare l'ordine di disegno. Per altre informazioni, vedere [elevazione dei privilegi visivi in Android](visualelement-elevation.md).
- Disabilitazione della modalità colore legacy in una [`VisualElement`](xref:Xamarin.Forms.VisualElement)supportata. Per altre informazioni, vedere [modalità colore legacy di visualElement in Android](legacy-color-mode.md).

Per le viste Novell. Forms in Android sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Utilizzando la spaziatura interna predefinita e i valori dell'ombreggiatura di pulsanti di Android. Per altre informazioni, vedere [riempimento dei pulsanti e ombre in Android](button-padding-shadow.md).
- Impostazione delle opzioni di Input Method Editor per la tastiera soft per un [`Entry`](xref:Xamarin.Forms.Entry). Per altre informazioni, vedere [Opzioni dell'editor del metodo Entry input in Android](entry-ime-options.md).
- Abilitazione di un'ombreggiatura in un `ImageButton`. Per altre informazioni, vedere [ImageButton Drop Shadows in Android](imagebutton-drop-shadow.md).
- Per ulteriori informazioni sull'abilitazione dello scorrimento rapido in un [`ListView`](xref:Xamarin.Forms.ListView) , vedere la pagina [relativa allo scorrimento rapido di ListView in Android](listview-fast-scrolling.md).
- Controllo della transizione utilizzata per l'apertura di un `SwipeView`. Per altre informazioni, vedere [modalità di transizione SwipeView swipe](swipeview-swipetransitionmode.md).
- Controllare se un [`WebView`](xref:Xamarin.Forms.WebView) può visualizzare contenuto misto. Per altre informazioni, vedere [WebView mixed content in Android](webview-mixed-content.md).
- Abilitazione dello zoom su un [`WebView`](xref:Xamarin.Forms.WebView). Per altre informazioni, vedere [zoom di WebView in Android](webview-zoom-controls.md).

Per le celle Novell. Forms in Android sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Abilitazione della modalità legacy di [`ViewCell`](xref:Xamarin.Forms.ViewCell) azioni del contesto, in modo che il menu azioni del contesto non venga aggiornato quando viene modificato l'elemento selezionato in una [`ListView`](xref:Xamarin.Forms.ListView) . Per altre informazioni, vedere [ViewCell context actions on Android](viewcell-context-actions.md).

Per le pagine Novell. Forms in Android sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Impostazione dell'altezza della barra di spostamento in un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Per altre informazioni, vedere [altezza della barra NavigationPage in Android](navigationpage-bar-height.md).
- Disabilitazione delle animazioni di transizione durante l'esplorazione delle pagine in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Per altre informazioni, vedere [TabbedPage page Transition animations on Android](tabbedpage-transition-animations.md).
- Abilitazione del swiping tra le pagine in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Per altre informazioni, vedere [TabbedPage page swiping on Android](tabbedpage-page-swiping.md).
- Impostazione della posizione e del colore della barra degli strumenti in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Per altre informazioni, vedere [selezione host e colore della barra degli strumenti TabbedPage in Android](tabbedpage-toolbar-placement-color.md).

Per la classe Novell. Forms [`Application`](xref:Xamarin.Forms.Application) in Android sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Impostazione della modalità operativa di una tastiera. Per altre informazioni, vedere [modalità di input tastiera soft in Android](soft-keyboard-input-mode.md).
- Disabilitare gli eventi del ciclo di vita della pagina [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) e [`Appearing`](xref:Xamarin.Forms.Page.Appearing) in pausa e riprendere rispettivamente per le applicazioni che usano AppCompat. Per altre informazioni, vedere [eventi del ciclo di vita della pagina in Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Piattaforme supportate

Originariamente, il progetto Android Novell. Forms predefinito usava uno stile precedente di rendering del controllo che era comune prima di Android 5,0. Le applicazioni compilate con il modello hanno `FormsApplicationActivity` come classe di base dell'attività principale.

## <a name="material-design-via-appcompat"></a>Progettazione materiale tramite AppCompat

I progetti Android Novell. Forms ora usano `FormsAppCompatActivity` come classe di base dell'attività principale. Questa classe usa le funzionalità di **AppCompat** fornite da Android per implementare i temi di progettazione del materiale.

Per aggiungere temi di progettazione del materiale al progetto Android Novell. Forms, seguire le [istruzioni di installazione per il supporto di AppCompat](appcompat-material-design.md)

Di seguito è riportato l'esempio **todo** con il `FormsApplicationActivity`predefinito:

[![](images/before-appcompat-sml.png "Todo Sample Application Without AppCompat")](images/before-appcompat.png#lightbox "Todo Sample Application Without AppCompat")

Si tratta dello stesso codice dopo l'aggiornamento del progetto per l'uso di `FormsAppCompatActivity` (e l'aggiunta delle informazioni aggiuntive sul tema):

[![](images/post-appcompat-sml.png "Todo Sample Application With AppCompat and Theming")](images/post-appcompat.png#lightbox "Todo Sample Application With AppCompat and Theming")

> [!NOTE]
> Quando si usa `FormsAppCompatActivity`, le [classi di base per alcuni renderer personalizzati Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) saranno diverse.

## <a name="androidx-migration"></a>Migrazione AndroidX

AndroidX sostituisce la libreria di supporto Android. Per informazioni su AndroidX e su come eseguire la migrazione di un'app Novell. Forms per usare le librerie AndroidX, vedere [migrazione di AndroidX in Novell. Forms](~/xamarin-forms/platform/android/androidx-migration.md).

## <a name="related-links"></a>Collegamenti correlati

- [Aggiunta del supporto per la progettazione di materiali](appcompat-material-design.md)
