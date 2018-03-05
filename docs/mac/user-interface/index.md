---
title: Interfaccia utente
description: Questo articolo include collegamenti a guide che descrivono macOS vari controlli dell'interfaccia utente.
ms.topic: article
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: a8cb9488849dafc2cd720ecf59d654009a9ad781
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="user-interface"></a>Interfaccia utente

_Questo articolo include collegamenti a guide che descrivono macOS vari controlli dell'interfaccia utente._

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere alla stessa controlli dell'interfaccia utente che uno sviluppatore che lavora *Objective-C* e *Xcode* does. Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare del Xcode _generatore interfaccia_ per creare e gestire le interfacce utente (o, facoltativamente, crearli direttamente nel codice c#). 

Le guide seguenti forniscono informazioni dettagliate sull'utilizzo di elementi dell'interfaccia utente macOS in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in ogni articolo.

Si consiglia di esaminare il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` comandi utilizzato per le classi c# in transito-fino a oggetti Objective-C e gli elementi dell'interfaccia utente.

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

In questo articolo viene descritto l'utilizzo con Windows e i pannelli in un'applicazione Xamarin.Mac. Tratta creazione e gestione di Windows e i pannelli in Xcode e interfaccia generatore, il caricamento di Windows e i pannelli da `.storyboard` o `.xib` i file, mediante Windows e risponde a Windows nel codice c#.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Dialogs](~/mac/user-interface/dialog.md) (Finestre di dialogo)

In questo articolo viene descritto l'utilizzo con le finestre di dialogo e finestre modali in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire le finestre modali in Xcode e Interface builder, usare le finestre di dialogo standard, visualizzare e rispondere alle finestre nel codice C#.

## <a name="alertsmacuser-interfacealertmd"></a>[Alerts](~/mac/user-interface/alert.md) (Avvisi)

In questo articolo viene descritto l'utilizzo con gli avvisi in un'applicazione Xamarin.Mac. Viene descritto come creare e visualizzare gli avvisi dal codice C# e rispondere agli avvisi.

## <a name="menusmacuser-interfacemenumd"></a>[Menu](~/mac/user-interface/menu.md)

Menu vengono utilizzati nelle varie parti dell'interfaccia utente dell'applicazione un Mac. dal menu principale dell'applicazione nella parte superiore dello schermo per i menu a comparsa e contestuali che possono essere visualizzati in un punto qualsiasi in una finestra. I menu sono parte integrante dell'esperienza utente di un'applicazione Mac. Questo articolo descrive l'uso dei menu Cocoa in un'applicazione Xamarin.Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Controlli standard](~/mac/user-interface/standard-controls.md)

Utilizzo di controlli AppKit standard, ad esempio pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati in un'applicazione Xamarin.Mac. Questa guida illustra aggiungerli a una progettazione dell'interfaccia utente in Generatore di interfaccia di Xcode, esporle al codice tramite punti vendita e le azioni e utilizzo dei controlli AppKit nel codice c#.

 
## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barre degli strumenti](~/mac/user-interface/toolbar.md)

In questo articolo viene descritto l'utilizzo con le barre degli strumenti in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire le barre degli strumenti in Xcode e Interface Builder, esporre gli elementi della barra degli strumenti al codice usando outlet e azioni, abilitare e disabilitare gli elementi della barra degli strumenti e rispondere agli elementi della barra degli strumenti nel codice C#.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Table Views](~/mac/user-interface/table-view.md) (Visualizzazioni tabelle)

In questo articolo viene descritto l'utilizzo con le visualizzazioni di tabella in un'applicazione Xamarin.Mac. Illustra la creazione e la Gestione viste delle tabelle in Xcode e interfaccia generatore, come esporre gli elementi della visualizzazione tabella al codice utilizzando punti vendita e le azioni, la compilazione di viste delle tabelle e infine risponde agli elementi di visualizzazione tabella nel codice c#.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Outline Views](~/mac/user-interface/outline-view.md) (Visualizzazioni struttura)

In questo articolo viene descritto l'utilizzo con visualizzazioni della struttura in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire le visualizzazioni Struttura in Xcode e Interface Builder, esporre gli elementi della visualizzazione Struttura al codice usando outlet e azioni, popolare gli elementi della struttura e rispondere agli elementi della visualizzazione Struttura nel codice C#.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Source Lists](~/mac/user-interface/source-list.md) (Elenchi di risorse)

In questo articolo viene descritto l'utilizzo degli elenchi di origine in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire gli elenchi di origine in Xcode e Interface Builder, esporre gli elementi degli elenchi di origine al codice usando outlet e azioni, popolare gli elementi degli elenchi di origine e rispondere agli elementi degli elenchi di origine nel codice C#.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Visualizzazioni raccolta](~/mac/user-interface/collection-view.md)

In questo articolo viene descritto l'utilizzo con le visualizzazioni di raccolta in un'applicazione Xamarin.Mac. Viene descritto come creare e gestire le visualizzazioni raccolta in Xcode e Interface Builder, esporre gli elementi della visualizzazione raccolta al codice usando outlet e azioni, popolare le visualizzazioni raccolta e rispondere alle visualizzazioni raccolta nel codice C#.

## <a name="creating-custom-user-controlsmacuser-interfacecustom-controlsmd"></a>[Creazione di controlli utente personalizzati](~/mac/user-interface/custom-controls.md)

Questo articolo vengono illustrate la creazione di controlli dell'interfaccia utente personalizzati (tramite l'eredità da `NSControl`), un'interfaccia personalizzata per il controllo di disegno e la creazione di azioni personalizzate che possono essere utilizzate con il generatore di interfaccia di Xcode.

## <a name="mac-samples-gallery"></a>Raccolta di esempi Mac

È consigliabile inoltre un'occhiata al [raccolta di esempi Mac](http://developer.xamarin.com/samples/mac/all/), include una vasta gamma di codice pronti all'uso che consentono di ottenere un progetto Xamarin.Mac off interamente rapidamente.

## <a name="related-links"></a>Collegamenti correlati

- [linee guida dell'interfaccia umana macOS](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
