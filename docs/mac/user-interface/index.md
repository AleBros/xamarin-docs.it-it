---
title: controlli dell'interfaccia utente macOS in xamarin. Mac
description: Questo documento include collegamenti alle guide che descrivono i vari controlli dell'interfaccia utente disponibili per gli sviluppatori di xamarin. Mac. Contenuto collegato si esaminano windows, le finestre di dialogo, avvisi, menu, barre degli strumenti, le visualizzazioni di tabelle, visualizzazioni della struttura e informazioni.
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/27/2018
ms.openlocfilehash: a12553cf0b7b9584bb8ff7bc04ed326ad4a7ad2a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107833"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>controlli dell'interfaccia utente macOS in xamarin. Mac

_Questo articolo contiene collegamenti alle guide che descrivono macOS vari controlli dell'interfaccia utente._

Quando si utilizza c# e .NET in un'applicazione xamarin. Mac, è possibile accedere allo stesso utente controlli dell'interfaccia che gli sviluppatori che lavorano *Objective-C* e *Xcode* viene. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Xcode _Interface Builder_ per creare e gestire le interfacce utente (oppure crearle direttamente nel codice c#).

Le guide elencate di seguito contengono informazioni dettagliate sull'uso di elementi dell'interfaccia utente macOS in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in tutti gli articoli.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) documentare anche, come viene spiegato il `Register` e `Export` attributi usati per wireup classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

Questo articolo descrive l'uso delle finestre e pannelli in un'applicazione xamarin. Mac. Viene descritto come creare e gestire le finestre e pannelli in Xcode e Interface Builder, il caricamento di windows e i pannelli da file con estensione xib o storyboard, usando windows e rispondere alle finestre nel codice c#.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Dialogs](~/mac/user-interface/dialog.md) (Finestre di dialogo)

Questo articolo descrive l'uso delle finestre di dialogo e finestre modali in un'applicazione xamarin. Mac. Descritto come creare e gestire le finestre modali in Xcode e Interface Builder, uso di finestre di dialogo standard e per visualizzare e rispondere alle finestre nel codice c#.

## <a name="alertsmacuser-interfacealertmd"></a>[Alerts](~/mac/user-interface/alert.md) (Avvisi)

Questo articolo descrive come usare gli avvisi in un'applicazione xamarin. Mac. Viene illustrata la creazione e la visualizzazione degli avvisi dal codice c# e rispondere agli avvisi.

## <a name="menusmacuser-interfacemenumd"></a>[Menu](~/mac/user-interface/menu.md)

I menu sono usati in diverse parti dell'interfaccia utente di un'applicazione Mac. dal menu principale dell'applicazione nella parte superiore dello schermo per i menu a comparsa e menu contestuali che possono essere visualizzati in un punto qualsiasi in una finestra. I menu sono parte integrante dell'esperienza utente di un'applicazione Mac. Questo articolo descrive l'uso dei menu Cocoa in un'applicazione xamarin. Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Controlli standard](~/mac/user-interface/standard-controls.md)

Utilizzo di controlli AppKit standard, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati in un'applicazione xamarin. Mac. Questa guida illustra aggiungerli a una progettazione dell'interfaccia utente in Interface Builder di Xcode, esponendoli al codice tramite Outlet e azioni e l'utilizzo di controlli AppKit nel codice c#.

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barre degli strumenti](~/mac/user-interface/toolbar.md)

Questo articolo descrive l'uso delle barre degli strumenti in un'applicazione xamarin. Mac. Viene descritto come creare e gestire le barre degli strumenti in Xcode e Interface Builder, esporre gli elementi della barra degli strumenti al codice usando Outlet e azioni, l'abilitazione e disabilitazione di elementi della barra degli strumenti e rispondere agli elementi della barra degli strumenti nel codice c#.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Visualizzazioni di tabelle](~/mac/user-interface/table-view.md)

Questo articolo descrive l'uso delle visualizzazioni tabella in un'applicazione xamarin. Mac. Descritto come creare e gestire le visualizzazioni di tabella in Xcode e Interface Builder, come per esporre la visualizzazione della tabella gli elementi di codice usando Outlet e azioni, popolare le visualizzazioni di tabelle e rispondere agli elementi della visualizzazione tabella nel codice c#.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Visualizzazione della struttura](~/mac/user-interface/outline-view.md)

Questo articolo descrive l'uso delle visualizzazioni struttura in un'applicazione xamarin. Mac. Descritto come creare e gestire le visualizzazioni struttura in Xcode e Interface Builder, la modalità di visualizzazione della struttura di esporre gli elementi al codice usando Outlet e azioni, popolare la visualizzazione della struttura e alle risposte per strutturare gli elementi di visualizzazione nel codice c#.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Elenchi di origine](~/mac/user-interface/source-list.md)

Questo articolo descrive come usare gli elenchi di origine in un'applicazione xamarin. Mac. Viene descritto come creare e gestire gli elenchi di origine in Xcode e Interface Builder, esporre gli elementi elenco di origine al codice usando Outlet e azioni, popolare gli elenchi di origine e rispondere agli elementi di elenco di origine nel codice c#.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Visualizzazioni di raccolta](~/mac/user-interface/collection-view.md)

Questo articolo descrive l'uso delle visualizzazioni raccolta in un'applicazione xamarin. Mac. Descritto come creare e gestire le visualizzazioni raccolta in Xcode e Interface Builder, come per esporre la visualizzazione di raccolta di elementi al codice usando Outlet e azioni, popolare le visualizzazioni raccolta e rispondere alle visualizzazioni raccolta nel codice c#.

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[Creazione di controlli personalizzati](~/mac/user-interface/custom-controls.md)

Questo articolo illustra la creazione di controlli dell'interfaccia utente personalizzata (ereditando da `NSControl`), un'interfaccia personalizzata per il controllo di disegno e la creazione di un'azione personalizzata che possono essere usata con Interface Builder di Xcode.

## <a name="mac-samples-gallery"></a>Raccolta di esempi di Mac

Si consiglia anche di esaminare i [raccolta di esempi Mac](https://developer.xamarin.com/samples/mac/all/). Include un'ampia gamma di codice pronti da usare che consenta di ottenere rapidamente un progetto xamarin. Mac il piede giusto.

## <a name="related-links"></a>Collegamenti correlati

- [Linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
