---
title: interfaccia utente macOS
description: Questo articolo include collegamenti a guide che descrivono macOS vari controlli dell'interfaccia utente.
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/27/2018
ms.openlocfilehash: d40faa29f2fe278377bf4eae42a032f3dc9086ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="macos-user-interface"></a>interfaccia utente macOS

_Questo articolo include collegamenti a guide che descrivono macOS vari controlli dell'interfaccia utente._

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere allo stesso utente controlli dell'interfaccia che gli sviluppatori che lavorano *Objective-C* e *Xcode* does. Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare del Xcode _generatore interfaccia_ per creare e gestire le interfacce utente (o, facoltativamente, crearli direttamente nel codice c#).

Le guide seguenti forniscono informazioni dettagliate sull'utilizzo di elementi dell'interfaccia utente macOS in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in ogni articolo.

È possibile esaminare la [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documento anche, come viene spiegato il `Register` e `Export` attributi utilizzati per le classi c# wire-fino a oggetti Objective-C e gli elementi dell'interfaccia utente.

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

In questo articolo viene descritto l'utilizzo con windows e i pannelli in un'applicazione Xamarin.Mac. Vengono illustrate la creazione e gestione di windows e pannelli in Xcode e il generatore di interfaccia, il caricamento di windows e pannelli dai file .storyboard o .xib, utilizzando windows e risponde a windows nel codice c#.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Dialogs](~/mac/user-interface/dialog.md) (Finestre di dialogo)

In questo articolo viene descritto l'utilizzo con le finestre di dialogo e finestre modali in un'applicazione Xamarin.Mac. Vengono illustrate la creazione e la gestione di finestre modali in Xcode e interfaccia generatore, utilizzo di finestre di dialogo standard e per visualizzare e rispondere a windows nel codice c#.

## <a name="alertsmacuser-interfacealertmd"></a>[Alerts](~/mac/user-interface/alert.md) (Avvisi)

In questo articolo viene descritto l'utilizzo con gli avvisi in un'applicazione Xamarin.Mac. Vengono illustrate la creazione e la visualizzazione degli avvisi dal codice c# e rispondere agli avvisi.

## <a name="menusmacuser-interfacemenumd"></a>[Menu](~/mac/user-interface/menu.md)

Menu vengono utilizzati nelle varie parti dell'interfaccia utente dell'applicazione un Mac. dal menu principale dell'applicazione nella parte superiore dello schermo per i menu a comparsa e menu di scelta rapida che possono essere visualizzati in un punto qualsiasi in una finestra. I menu sono parte integrante dell'esperienza utente dell'applicazione un Mac. In questo articolo viene descritto l'utilizzo con i menu Cocoa in un'applicazione Xamarin.Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Controlli standard](~/mac/user-interface/standard-controls.md)

Utilizzo di controlli quali pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati in un'applicazione Xamarin.Mac AppKit standard. Questa guida illustra aggiungerli a una progettazione dell'interfaccia utente in Generatore di interfaccia di Xcode, esporle al codice tramite commercializzano presso i punti e le azioni e l'utilizzo di controlli AppKit nel codice c#.

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barre degli strumenti](~/mac/user-interface/toolbar.md)

In questo articolo viene descritto l'utilizzo con le barre degli strumenti in un'applicazione Xamarin.Mac. Illustra la creazione e la gestione di barre degli strumenti in Xcode e il generatore di interfaccia, come esporre gli elementi della barra degli strumenti al codice utilizzando commercializzano presso i punti e le azioni, l'abilitazione e disabilitazione di elementi della barra degli strumenti e infine risponde agli elementi della barra degli strumenti nel codice c#.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Viste delle tabelle](~/mac/user-interface/table-view.md)

In questo articolo viene descritto l'utilizzo con le visualizzazioni di tabella in un'applicazione Xamarin.Mac. Tratta la creazione e la gestione di viste delle tabelle in Xcode e interfaccia generatore, come per esporre la visualizzazione della tabella items al codice usando commercializzano presso i punti e le azioni, popolamento viste delle tabelle e alle risposte agli elementi di visualizzazione tabella nel codice c#.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Visualizzazioni della struttura](~/mac/user-interface/outline-view.md)

In questo articolo viene descritto l'utilizzo con visualizzazioni della struttura in un'applicazione Xamarin.Mac. Tratta la creazione e la gestione di visualizzazioni della struttura in Xcode e interfaccia generatore, come per esporre la visualizzazione struttura degli elementi al codice usando commercializzano presso i punti e le azioni, popolamento visualizzazioni della struttura e alle risposte per strutturare gli elementi di visualizzazione nel codice c#.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Elenchi di origine](~/mac/user-interface/source-list.md)

In questo articolo viene descritto l'utilizzo con gli elenchi di origine in un'applicazione Xamarin.Mac. Tratta la creazione e la gestione di elenchi di origine in Xcode e interfaccia generatore, come esporre gli elementi dell'elenco origine codice utilizzando commercializzano presso i punti e le azioni, al popolamento gli elenchi di origine e risponde alle voci di elenco di origine nel codice c#.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Viste di raccolta](~/mac/user-interface/collection-view.md)

In questo articolo viene descritto l'utilizzo con le viste di raccolta in un'applicazione Xamarin.Mac. Tratta la creazione e la gestione di viste di raccolta in Xcode e interfaccia generatore, come per esporre la visualizzazione della raccolta items al codice usando commercializzano presso i punti e le azioni, la compilazione di viste di raccolta e risponde alle visualizzazioni di raccolta nel codice c#.

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[Creazione di controlli personalizzati](~/mac/user-interface/custom-controls.md)

Questo articolo vengono illustrate la creazione di controlli dell'interfaccia utente personalizzata (tramite l'eredità da `NSControl`), un'interfaccia personalizzata per il controllo di disegno e la creazione di azioni personalizzate che possono essere utilizzate con generatore del Xcode di interfaccia.

## <a name="mac-samples-gallery"></a>Raccolta di esempi Mac

È inoltre consigliabile osservare il [della raccolta di esempi Mac](https://developer.xamarin.com/samples/mac/all/). Include una vasta gamma di codice pronti per l'uso che consentono di ottenere rapidamente un progetto Xamarin.Mac off interamente.

## <a name="related-links"></a>Collegamenti correlati

- [linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
