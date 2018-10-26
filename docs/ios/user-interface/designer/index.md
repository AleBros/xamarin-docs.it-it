---
title: Creazione di interfacce utente con iOS Designer
description: Questo documento descrive come usare la finestra di progettazione di Xamarin per iOS per compilare un'interfaccia utente di app con file con estensione xib e storyboard. Collega a documenti che disponibilità dello strumento, le funzionalità di base, i controlli manipolati, forniscono informazioni e procedure dettagliate del suo utilizzo.
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/31/2018
ms.openlocfilehash: 7c6529c539ab502fb6c13226acd18a57f8f6d58e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109744"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>Creazione di interfacce utente con iOS Designer

_La finestra di progettazione di Xamarin per iOS è una finestra di progettazione visiva per iOS Storyboard e Interface Builder formati che è completamente integrato con Visual Studio per Mac e Visual Studio. IOS Designer mantiene la compatibilità completa con i formati di Storyboard e xib, in modo che i file possono essere modificati in Visual Studio per Mac o Visual Studio oltre a Interface Builder di Xcode. Inoltre, la finestra di progettazione di Xamarin per iOS supporta funzionalità avanzate, ad esempio i controlli personalizzati che eseguono il rendering in fase di progettazione nell'editor._

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![iOS Designer in Visual Studio per Mac](images/designer-vsmac-sml.png "iOS Designer")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![iOS Designer in Visual Studio](images/designer-vs.png "iOS Designer")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>Disponibilità

La finestra di progettazione di Xamarin per iOS è disponibile in Visual Studio per Mac e in Visual Studio 2017 in Windows.

Queste guide presuppone una certa familiarità con il contenuto illustrato nel [Guida introduttiva xamarin. IOS](~/ios/get-started/index.md).

## <a name="ios-designer-basicsintroductionmd"></a>[Nozioni di base su iOS Designer](introduction.md)

Questa guida illustra le funzionalità di Xamarin iOS designer. Vengono descritti i concetti di base della finestra di progettazione, che mostra come usare la finestra di progettazione per disporre i controlli in modo visivo e come modificare le proprietà.

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[Cenni preliminari sui controlli manipolati](ios-designable-controls-overview.md)

Questa guida è simile in profondità di controlli personalizzati, come vengono creati e i requisiti che devono soddisfare per essere eseguito il rendering nell'area di progettazione. Inoltre, viene illustrato come eseguire il debug di problemi comuni che possono verificarsi quando si usano controlli manipolati.

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[Procedura dettagliata: uso di controlli personalizzati con iOS Designer](ios-designable-controls-walkthrough.md)

Questo articolo fornisce una procedura dettagliata che illustra come creare un controllo personalizzato e usarlo in iOS designer. Viene illustrato come rendere disponibile nella casella degli strumenti della finestra di progettazione in modo che sia possibile trascinare/rilasciarla in una visualizzazione di un controllo. Inoltre, viene spiegato come implementare un controllo, ne esegue il rendering correttamente in fase di progettazione e di runtime, nonché come creare proprietà che può essere impostata in fase di progettazione.

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[Layout automatico con Xamarin iOS Designer](designer-auto-layout.md)

Questa guida presenta iOS Layout automatico e il nuovo flusso di lavoro vincoli disponibile in iOS designer.
