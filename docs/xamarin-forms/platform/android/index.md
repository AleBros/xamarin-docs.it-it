---
title: Funzionalità della piattaforma Android
description: Questo articolo illustra come aggiungere funzionalità specifiche di Android per le applicazioni xamarin. Forms.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2018
ms.openlocfilehash: 72a55441658755c1e068b33f910f3b7c0db5502f
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207778"
---
# <a name="android-platform-features"></a>Funzionalità della piattaforma Android

Sviluppo di applicazioni xamarin. Forms per Android richiede Visual Studio. Il [pagina dei requisiti](~/xamarin-forms/get-started/installation.md) contiene altre informazioni sui prerequisiti.

## <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti.

Le funzionalità specifiche della piattaforma seguenti viene fornita per xamarin. Forms visualizzazioni pagine e i layout in Android:

- Controllare l'ordine Z degli elementi visivi per determinare l'ordine di disegno. Per altre informazioni, vedere [VisualElement l'elevazione dei privilegi in Android](visualelement-elevation.md).
- Disattivazione della modalità colore legacy in supportato [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [modalità di colore VisualElement Legacy in Android](legacy-color-mode.md).

Le funzionalità specifiche della piattaforma seguenti viene fornita per le visualizzazioni di xamarin. Forms in Android:

- Utilizzando la spaziatura interna predefinita e i valori dell'ombreggiatura di pulsanti di Android. Per altre informazioni, vedere [pulsante spaziatura interna e ombreggiature in Android](button-padding-shadow.md).
- L'impostazione di opzioni dell'editor per la tastiera per il metodo di input un [ `Entry` ](xref:Xamarin.Forms.Entry). Per altre informazioni, vedere [voce Input Method Editor opzioni in Android](entry-ime-options.md).
- Abilitare un'ombreggiatura in una `ImageButton`. Per altre informazioni, vedere [ImageButton ombreggiature in Android](imagebutton-drop-shadow.md).
- Abilitare lo scorrimento rapido in un [ `ListView` ](xref:Xamarin.Forms.ListView) per altre informazioni, vedere [ListView scorrimento rapido in Android](listview-fast-scrolling.md).
- Controllare se un [ `WebView` ](xref:Xamarin.Forms.WebView) può visualizzare il contenuto misto. Per altre informazioni, vedere [WebView il contenuto misto in Android](webview-mixed-content.md).

Le funzionalità specifiche della piattaforma seguenti viene fornita per le pagine di xamarin. Forms in Android:

- L'impostazione dell'altezza della barra di spostamento in un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Per altre informazioni, vedere [altezza delle barre NavigationPage in Android](navigationpage-bar-height.md).
- La disabilitazione di animazioni di transizione durante lo spostamento tra le pagine in un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Per altre informazioni, vedere [animazioni di transizione pagina TabbedPage in Android](tabbedpage-transition-animations.md).
- Abilitazione scorrendo rapidamente tra le pagine in un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Per altre informazioni, vedere [TabbedPage pagina scorrendo rapidamente in Android](tabbedpage-page-swiping.md).
- Impostare il posizionamento della barra degli strumenti e il colore in una [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Per altre informazioni, vedere [TabbedPage sulla barra degli strumenti posizione e il colore in Android](tabbedpage-toolbar-placement-color.md).

Le funzionalità specifiche della piattaforma seguenti viene fornita per xamarin. Forms [ `Application` ](xref:Xamarin.Forms.Application) classe in Android:

- Impostazione della modalità operativa di una tastiera. Per altre informazioni, vedere [temporaneamente in modalità di Input da tastiera in Android](soft-keyboard-input-mode.md).
- La disabilitazione il [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) pagina degli eventi del ciclo di vita in pausa e riprendere rispettivamente per le applicazioni che usano AppCompat. Per altre informazioni, vedere [gli eventi del ciclo di vita di pagina in Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Supporto per piattaforme

Il progetto Android di xamarin. Forms predefinito usato in origine, uno stile meno recente di rendering di controlli che è stata comuni prima Android 5.0. Le applicazioni create utilizzando il modello hanno `FormsApplicationActivity` come classe di base della loro attività principale.

## <a name="material-design-via-appcompat"></a>Progettazione dei materiali tramite AppCompat

Progetti di Android di xamarin. Forms utilizzano ora `FormsAppCompatActivity` come classe di base della loro attività principale. Questa classe viene utilizzata **AppCompat** funzionalità fornite da Android per implementare i temi di Material Design.

Per aggiungere i temi di Material Design al progetto Android di xamarin. Forms, seguire la [supportano le istruzioni di installazione per AppCompat](appcompat-material-design.md)

Di seguito è riportato il **Todo** esempio con il valore predefinito `FormsApplicationActivity`:

[![](images/before-appcompat-sml.png "Applicazione di esempio TODO senza AppCompat")](images/before-appcompat.png#lightbox "applicazione di esempio Todo senza AppCompat")

E questo è lo stesso codice dopo l'aggiornamento del progetto usare `FormsAppCompatActivity` (e aggiungere le informazioni sul tema aggiuntivi):

[![](images/post-appcompat-sml.png "Applicazione di esempio TODO con AppCompat e dei temi")](images/post-appcompat.png#lightbox "applicazione di esempio Todo con AppCompat e dei temi")

> [!NOTE]
> Quando si usa `FormsAppCompatActivity`, il [classi di base per alcuni renderer personalizzati Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) sarà diverso.

## <a name="related-links"></a>Collegamenti correlati

- [Aggiungere il supporto di Material Design](appcompat-material-design.md)
