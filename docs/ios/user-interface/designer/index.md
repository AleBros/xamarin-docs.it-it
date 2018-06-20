---
title: Creazione di interfacce utente con la finestra di progettazione iOS
description: Questo documento viene descritto come utilizzare la finestra di progettazione di Xamarin per iOS per compilare l'interfaccia utente dell'app con storyboard e i file .xib. Fornisca un collegamento ai documenti che illustrano la disponibilità dello strumento, la funzionalità di base, i controlli possa e forniscono procedure dettagliate della sua applicazione.
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/31/2018
ms.openlocfilehash: a931373a6abba3084af3c7aefcdddc903ad1b577
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209232"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>Creazione di interfacce utente con la finestra di progettazione iOS

_La finestra di progettazione di Xamarin per iOS è una finestra di progettazione visiva per iOS Storyboard e interfaccia generatore formati che è completamente integrato con Visual Studio per Mac e Visual Studio. La finestra di progettazione iOS gestisce completamente compatibile con i formati di Storyboard e .xib, in modo che i file possono essere modificati in Visual Studio per Mac o Visual Studio, oltre a interfaccia generatore del Xcode. Inoltre, la finestra di progettazione Xamarin iOS supporta funzionalità avanzate, quali controlli personalizzati che eseguono il rendering in fase di progettazione nell'editor._

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![iOS progettazione di Visual Studio per Mac](images/designer-vsmac-sml.png "iOS progettazione")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Progettazione di Visual Studio iOS](images/designer-vs.png "iOS progettazione")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>Disponibilità

La finestra di progettazione di Xamarin per iOS è disponibile in Visual Studio per Mac e in Visual Studio 2017 in Windows.

Queste guide presuppone una certa familiarità con il contenuto incluso nel [xamarin. IOS Getting Started Guide](~/ios/get-started/index.md).

## <a name="ios-designer-basicsintroductionmd"></a>[Nozioni di base su iOS Designer](introduction.md)

Questa guida illustra le funzionalità della finestra di progettazione iOS Xamarin. Illustra i concetti di base della finestra di progettazione che mostra come utilizzare la finestra di progettazione per disporre i controlli visivamente e come modificare le proprietà.

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[Cenni preliminari sui controlli possa](ios-designable-controls-overview.md)

Questa Guida ricerca in profondità di controlli personalizzati, come vengono creati e i requisiti che devono essere soddisfatti per eseguire il rendering nell'area di progettazione. Inoltre, viene illustrato come eseguire il debug di problemi comuni che possono verificarsi quando si utilizzano controlli essere progettato.

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[Procedura dettagliata: utilizzo di controlli personalizzati con progettazione iOS](ios-designable-controls-walkthrough.md)

Questo articolo fornisce una procedura dettagliata che illustra come creare un controllo personalizzato e utilizzarlo nella finestra di progettazione iOS. Viene illustrato come creare un controllo disponibile nella casella degli strumenti della finestra di progettazione in modo che può essere trascinamento/rilasciato in una vista. Inoltre, viene illustrato come implementare un controllo in modo corretto rendering in fase di progettazione e in fase di esecuzione, nonché come creare proprietà che può essere impostata in fase di progettazione.

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[Layout automatico con Xamarin iOS progettazione](designer-auto-layout.md)

Questa guida vengono presentate iOS Layout automatico e il nuovo flusso di lavoro vincoli disponibile nella finestra di progettazione iOS.
