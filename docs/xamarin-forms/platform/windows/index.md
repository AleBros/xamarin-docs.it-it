---
title: Funzionalità della piattaforma Windows
description: Questo articolo illustra il supporto della piattaforma Windows disponibile in xamarin. Forms.
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: 9367e22ede733ee2d93feccc001836fb4dc02564
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507110"
---
# <a name="windows-platform-features"></a>Funzionalità della piattaforma Windows

Sviluppo di applicazioni xamarin. Forms per le piattaforme Windows richiede Visual Studio. Il [pagina dei requisiti](~/get-started/requirements.md) contiene altre informazioni sui prerequisiti.

![](images/allhanselman.png "Applicazioni xamarin. Forms in esecuzione su Windows")

## <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti.

Le funzionalità specifiche della piattaforma seguenti viene fornita per xamarin. Forms visualizzazioni pagine e i layout in Universal Windows Platform (UWP):

- L'impostazione di una chiave di accesso per un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [VisualElement chiavi di accesso Windows](visualelement-access-keys.md).
- Disattivazione della modalità colore legacy in supportato [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [modalità di colore VisualElement Legacy in Windows](legacy-color-mode.md).

Le funzionalità specifiche della piattaforma seguenti viene fornita per le visualizzazioni di xamarin. Forms nella piattaforma UWP:

- Rilevamento di ordine di lettura dal testo contenuto nel [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) istanze. Per altre informazioni, vedere [ordine di lettura InputView su Windows](inputview-reading-order.md).
- Abilitazione del supporto di movimenti tocco in una [ `ListView` ](xref:Xamarin.Forms.ListView). Per altre informazioni, vedere [ListView SelectionMode su Windows](listview-selectionmode.md).
- Abilitazione di un [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) per interagire con il motore di controllo ortografico. Per altre informazioni, vedere [SearchBar controllo ortografico in Windows](searchbar-spell-check.md).
- Abilitazione di un [ `WebView` ](xref:Xamarin.Forms.WebView) per visualizzare gli avvisi di JavaScript in una finestra di dialogo messaggio UWP. Per altre informazioni, vedere [gli avvisi di JavaScript di WebView in Windows](webview-javascript-alert.md).

Le funzionalità specifiche della piattaforma seguenti viene fornita per le pagine di xamarin. Forms nella piattaforma UWP:

- Comprimere il [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) barra di spostamento. Per altre informazioni, vedere [MasterDetailPage sulla barra di spostamento a Windows](masterdetailpage-navigation-bar.md).
- Impostazione delle opzioni di posizionamento della barra degli strumenti. Per altre informazioni, vedere [posizione della barra degli strumenti pagina su Windows](page-toolbar-placement.md).
- Abilitazione di icone da visualizzare in una [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) sulla barra degli strumenti. Per altre informazioni, vedere [icone TabbedPage sul Windows](tabbedpage-icons.md).

## <a name="platform-support"></a>Supporto per piattaforme

I modelli di xamarin. Forms disponibili in Visual Studio contengono un progetto Universal Windows Platform (UWP).

> [!NOTE]
> Supporto di xamarin. Forms 1.x e 2.x _Windows Phone 8 Silverlight_, _Windows Phone 8.1_, e _di Windows 8.1_ lo sviluppo di applicazioni. Tuttavia, questi tipi di progetto sono stati deprecati.

## <a name="getting-started"></a>Per iniziare

Passare a **File > Nuovo > progetto** in Visual Studio e scegliere una del **multipiattaforma > App vuota (xamarin. Forms)** modelli per iniziare.

Soluzioni più vecchie in xamarin. Forms o quelli creati in macOS, non avrà tutti i progetti di Windows elencati in precedenza (ma devono essere aggiunti manualmente). Se la piattaforma Windows di destinazione non è già nella soluzione, visitare il [istruzioni di configurazione](installation/index.md) aggiungere tipo o i tipi di progetto di Windows desiderata.

## <a name="samples"></a>Esempi

[Tutti i campioni](https://github.com/xamarin/xamarin-forms-book-preview-2) Book Petzolds [ *creazione di App per dispositivi mobili con xamarin. Forms* ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) includono progetti di Universal Windows Platform (per Windows 10).

Il [app demo per "Scott Hanselman"](https://github.com/jamesmontemagno/Hanselman.Forms) è disponibile separatamente e includeranno anche i progetti di Apple Watch e Android Wear (rispettivamente tramite xamarin. IOS e xamarin. Android, xamarin. Forms non viene eseguito in tali piattaforme).

## <a name="related-links"></a>Collegamenti correlati

- [Progetti di Windows il programma di installazione](~/xamarin-forms/platform/windows/installation/index.md)
