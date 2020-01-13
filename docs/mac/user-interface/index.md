---
title: controlli dell'interfaccia utente macOS in Xamarin.Mac
description: Questo documento include collegamenti a guide che descrivono i vari controlli dell'interfaccia utente disponibili per gli sviluppatori Xamarin.Mac. Il contenuto collegato esamina le finestre, le finestre di dialogo, gli avvisi, i menu, le barre degli strumenti, le visualizzazioni di tabelle, le visualizzazioni struttura e altro ancora.
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 7f5303cd63c6ff1433b56b3f47b67d3925b1d1e1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032788"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>controlli dell'interfaccia utente macOS in Xamarin.Mac

_Questo articolo contiene collegamenti a guide che descrivono diversi controlli dell'interfaccia utente di macOS._

Quando si lavora C# con e .NET in un'applicazione Xamarin.Mac, è possibile accedere agli stessi controlli dell'interfaccia utente che uno sviluppatore lavora in *Objective-C* e *Xcode* . Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile usare _Interface Builder_ di Xcode per creare e gestire le interfacce utente oppure, facoltativamente, crearle direttamente C# nel codice.

Le guide elencate di seguito forniscono informazioni dettagliate sull'uso degli elementi dell'interfaccia utente macOS in un'applicazione Xamarin.Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in ogni articolo.

Si consiglia di esaminare la sezione relativa all' [esposizione di C# classi/metodi in Objective-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) del documento interno di [Xamarin.Mac](~/mac/internals/how-it-works.md) , perché spiega gli attributi`Register`e`Export`usati per collegare le C# classi a Oggetti Objective-C ed elementi dell'interfaccia utente.

## <a name="windowsmacuser-interfacewindowmd"></a>[Windows](~/mac/user-interface/window.md)

Questo articolo illustra l'uso di finestre e pannelli in un'applicazione Xamarin.Mac. Viene illustrata la creazione e la gestione di finestre e pannelli in Xcode e Interface Builder, il caricamento di finestre e pannelli da file con estensione storyboard o XIB, l'utilizzo C# di Windows e la risposta a Windows nel codice.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Dialogs](~/mac/user-interface/dialog.md) (Finestre di dialogo)

Questo articolo illustra l'uso di finestre di dialogo e finestre modali in un'applicazione Xamarin.Mac. Viene illustrata la creazione e la gestione di finestre modali in Xcode e Interface Builder, l'utilizzo di finestre di dialogo standard e la C# visualizzazione e la risposta a Windows nel codice.

## <a name="alertsmacuser-interfacealertmd"></a>[Alerts](~/mac/user-interface/alert.md) (Avvisi)

Questo articolo illustra l'uso degli avvisi in un'applicazione Xamarin.Mac. Viene illustrata la creazione e la C# visualizzazione di avvisi dal codice e la risposta agli avvisi.

## <a name="menusmacuser-interfacemenumd"></a>[Menu](~/mac/user-interface/menu.md)

I menu vengono usati in varie parti dell'interfaccia utente di un'applicazione Mac; dal menu principale dell'applicazione nella parte superiore della schermata ai menu popup e ai menu contestuali che possono essere visualizzati in qualsiasi punto di una finestra. I menu sono parte integrante dell'esperienza utente di un'applicazione Mac. Questo articolo illustra l'uso dei menu Cocoa in un'applicazione Xamarin.Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Controlli standard](~/mac/user-interface/standard-controls.md)

Usare i controlli AppKit standard come pulsanti, etichette, campi di testo, caselle di controllo e controlli segmentati in un'applicazione Xamarin.Mac. Questa guida illustra come aggiungerli a una progettazione dell'interfaccia utente in Interface Builder di Xcode, esponendoli al codice tramite Outlet e azioni e lavorando con i controlli C# AppKit nel codice.

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barre degli strumenti](~/mac/user-interface/toolbar.md)

Questo articolo illustra l'uso delle barre degli strumenti in un'applicazione Xamarin.Mac. Viene illustrata la creazione e la gestione delle barre degli strumenti in Xcode e Interface Builder, come esporre gli elementi della barra degli strumenti al codice usando Outlet e azioni, abilitare e disabilitare gli elementi della barra C# degli strumenti e infine rispondere agli elementi della barra degli strumenti nel codice.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Viste tabella](~/mac/user-interface/table-view.md)

Questo articolo illustra l'uso delle visualizzazioni tabella in un'applicazione Xamarin.Mac. Viene illustrata la creazione e la gestione di viste tabella in Xcode e Interface Builder, come esporre gli elementi della visualizzazione tabella al codice usando Outlet e azioni, popolare le visualizzazioni di tabella e rispondere C# agli elementi della visualizzazione tabella nel codice.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Visualizzazione struttura](~/mac/user-interface/outline-view.md)

Questo articolo illustra l'uso delle visualizzazioni struttura in un'applicazione Xamarin.Mac. Viene illustrata la creazione e la gestione delle visualizzazioni struttura in Xcode e Interface Builder, come esporre gli elementi della visualizzazione struttura al codice usando Outlet e azioni, popolare le visualizzazioni struttura e rispondere agli C# elementi della visualizzazione struttura nel codice.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Elenchi di origine](~/mac/user-interface/source-list.md)

Questo articolo illustra l'uso degli elenchi di origine in un'applicazione Xamarin.Mac. Viene illustrata la creazione e la gestione degli elenchi di origine in Xcode e Interface Builder, come esporre gli elementi dell'elenco di origine al codice usando Outlet e azioni, popolare gli elenchi di C# origine e rispondere agli elementi dell'elenco di origine nel codice.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Viste di raccolta](~/mac/user-interface/collection-view.md)

Questo articolo illustra l'uso delle visualizzazioni di raccolta in un'applicazione Xamarin.Mac. Viene illustrata la creazione e la gestione delle visualizzazioni di raccolta in Xcode e Interface Builder, come esporre gli elementi della visualizzazione raccolta al codice usando Outlet e azioni, popolando le visualizzazioni di C# raccolta e rispondendo alle visualizzazioni di raccolta nel codice.

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[Creazione di controlli personalizzati](~/mac/user-interface/custom-controls.md)

Questo articolo illustra la creazione di controlli dell'interfaccia utente personalizzati (ereditando da `NSControl`), il disegno di un'interfaccia personalizzata per il controllo e la creazione di azioni personalizzate che possono essere usate con Interface Builder di Xcode.

## <a name="mac-samples-gallery"></a>Raccolta di esempi Mac

È inoltre consigliabile esaminare la [raccolta di esempi Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac). Include una vasta gamma di codice pronto per l'uso che può aiutarti a ottenere rapidamente un progetto Xamarin.Mac.

## <a name="related-links"></a>Collegamenti correlati

- [Linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
