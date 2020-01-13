---
title: Funzionalità della piattaforma Windows
description: Questo articolo illustra il supporto della piattaforma Windows disponibile in Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: 0e2db2a054c871668b5787a53ffbe4464f982174
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696926"
---
# <a name="windows-platform-features"></a>Funzionalità della piattaforma Windows

Per lo sviluppo di applicazioni Xamarin.Forms per piattaforme Windows è necessario Visual Studio. La [pagina requisiti](~/get-started/requirements.md) contiene altre informazioni sui prerequisiti.

![](images/allhanselman.png "Xamarin.Forms Applications Running on Windows")

## <a name="platform-specifics"></a>Specifiche della piattaforma

Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati.

Le seguenti funzionalità specifiche della piattaforma sono disponibili per le visualizzazioni, le pagine e i layout di Xamarin.Forms nella piattaforma UWP (Universal Windows Platform) (UWP):

- Impostazione di una chiave di accesso per un [`VisualElement`](xref:Xamarin.Forms.VisualElement). Per ulteriori informazioni, vedere la pagina [relativa alle chiavi di accesso di visualElement in Windows](visualelement-access-keys.md).
- Disabilitazione della modalità colore legacy in una [`VisualElement`](xref:Xamarin.Forms.VisualElement)supportata. Per altre informazioni, vedere [modalità colore legacy di visualElement in Windows](legacy-color-mode.md).

Per le viste Xamarin.Forms in UWP sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Rilevamento dell'ordine di lettura dal contenuto di testo nelle istanze [`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor)e [`Label`](xref:Xamarin.Forms.Label) . Per ulteriori informazioni, vedere l' [ordine di lettura di InputView in Windows](inputview-reading-order.md).
- Abilitazione del supporto del movimento tap in un [`ListView`](xref:Xamarin.Forms.ListView). Per ulteriori informazioni, vedere [ListView SelectionMode in Windows](listview-selectionmode.md).
- Abilitazione della direzione di pull di un `RefreshView` da modificare. Per ulteriori informazioni, vedere [RefreshView pull direction in Windows](refreshview-pulldirection.md).
- Consentire a un [`SearchBar`](xref:Xamarin.Forms.SearchBar) di interagire con il motore di controllo ortografico. Per ulteriori informazioni, vedere [SearchBar controllo ortografico in Windows](searchbar-spell-check.md).
- Abilitazione di un [`WebView`](xref:Xamarin.Forms.WebView) per visualizzare gli avvisi JavaScript in una finestra di dialogo del messaggio UWP. Per altre informazioni, vedere [WebView javascript Alerts on Windows](webview-javascript-alert.md).

Per le pagine Xamarin.Forms in UWP sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Compressione della barra di spostamento [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) . Per ulteriori informazioni, vedere [barra di spostamento MasterDetailPage in Windows](masterdetailpage-navigation-bar.md).
- Impostazione delle opzioni di posizionamento della barra degli strumenti. Per ulteriori informazioni, vedere [posizionamento della barra degli strumenti delle pagine in Windows](page-toolbar-placement.md).
- Abilitazione delle icone di pagina da visualizzare su una barra degli strumenti [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Per altre informazioni, vedere [Icone TabbedPage in Windows](tabbedpage-icons.md).

## <a name="platform-support"></a>Supporto per piattaforme

I modelli Xamarin.Forms disponibili in Visual Studio contengono un progetto piattaforma UWP (Universal Windows Platform) (UWP).

> [!NOTE]
> Xamarin.Forms 1. x e 2. x supportano _Windows Phone 8 Silverlight_, _Windows Phone 8,1_e _Windows 8.1_ lo sviluppo di applicazioni. Tuttavia, questi tipi di progetto sono stati deprecati.

## <a name="getting-started"></a>Per iniziare

Per iniziare, passare a **File > nuovo progetto di >** in Visual Studio e scegliere uno dei modelli **multipiattaforma > App vuota (Xamarin.Forms)** .

Le soluzioni Xamarin.Forms precedenti, o quelle create in macOS, non avranno tutti i progetti Windows elencati sopra (ma devono essere aggiunti manualmente). Se la piattaforma Windows di destinazione non è già presente nella soluzione, vedere le [istruzioni di installazione](installation/index.md) per aggiungere il tipo o i tipi di progetto Windows desiderati.

## <a name="samples"></a>Esempi

[Tutti gli esempi per la](https://github.com/xamarin/xamarin-forms-book-preview-2) creazione di app per dispositivi mobili di Charles Petzold [*con Xamarin.Forms*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) includono i progetti piattaforma UWP (Universal Windows Platform) (per Windows 10).

L' [app demo "Scott hanselr"](https://github.com/jamesmontemagno/Hanselman.Forms) è disponibile separatamente e include anche Apple Watch e Android Wear Projects (usando rispettivamente Xamarin.iOS e Xamarin.Android, Xamarin.Forms non viene eseguito su tali piattaforme).

## <a name="related-links"></a>Collegamenti correlati

- [Imposta progetti Windows](~/xamarin-forms/platform/windows/installation/index.md)
