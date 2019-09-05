---
title: Creazione di interfacce utente con iOS designer
description: Questo documento descrive come usare la Xamarin Designer per iOS per compilare l'interfaccia utente di un'app con storyboard e file con estensione XIB. Sono inclusi collegamenti a documenti che illustrano la disponibilità dello strumento, le funzionalità di base, i controlli progettabili e forniscono procedure dettagliate relative all'uso.
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/31/2018
ms.openlocfilehash: 577c5602c1cbc331564c3034b3f0c11a4b97bc0c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279849"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>Creazione di interfacce utente con iOS designer

_Il Xamarin Designer per iOS è una finestra di progettazione visiva per lo storyboard iOS e Interface Builder formati completamente integrati con Visual Studio per Mac e Visual Studio. IOS designer mantiene la compatibilità completa con i formati storyboard e XIB, in modo che sia possibile modificare i file in Visual Studio per Mac o Visual Studio oltre ai Interface Builder di Xcode. Inoltre, il Xamarin Designer per iOS supporta funzionalità avanzate, ad esempio controlli personalizzati che eseguono il rendering in fase di progettazione nell'editor._

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![iOS designer in Visual Studio per Mac](images/designer-vsmac-sml.png "IOS designer")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![iOS designer in Visual Studio](images/designer-vs.png "IOS designer")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>Disponibilità

Il Xamarin Designer per iOS è disponibile in Visual Studio per Mac e in Visual Studio 2017 in Windows.

Queste guide presuppongono una certa familiarità con il contenuto trattato nelle [guide Introduzione Novell. iOS](~/ios/get-started/index.md).

## <a name="ios-designer-basicsintroductionmd"></a>[Nozioni di base su iOS Designer](introduction.md)

Questa guida illustra le funzionalità di Novell iOS designer. Illustra le nozioni di base di progettazione, mostrando come usare la finestra di progettazione per disporre i controlli in modo visivo e come modificare le proprietà.

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[Cenni preliminari sui controlli designable](ios-designable-controls-overview.md)

In questa guida vengono esaminati in dettaglio i controlli personalizzati, il modo in cui vengono creati e i requisiti che devono soddisfare per essere sottoposti a rendering nell'area di progettazione. Viene inoltre illustrato come eseguire il debug di problemi comuni che possono verificarsi quando si utilizzano controlli progettabili.

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[Procedura dettagliata: uso di controlli personalizzati con iOS designer](ios-designable-controls-walkthrough.md)

Questo articolo fornisce una procedura dettagliata che illustra come creare un controllo personalizzato e usarlo in iOS designer. Viene illustrato come rendere disponibile un controllo nella casella degli strumenti della finestra di progettazione in modo che possa essere trascinato in una visualizzazione. Viene inoltre illustrato come implementare un controllo in modo che venga eseguito correttamente in fase di progettazione e in fase di esecuzione, nonché come creare proprietà che possono essere impostate in fase di progettazione.

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[Layout automatico con Novell iOS designer](designer-auto-layout.md)

Questa guida presenta il layout automatico iOS e il nuovo flusso di lavoro dei vincoli disponibile in iOS designer.
