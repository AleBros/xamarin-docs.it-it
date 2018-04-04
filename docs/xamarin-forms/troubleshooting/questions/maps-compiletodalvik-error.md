---
title: Motivo per cui il progetto Xamarin.Forms.Maps Android esito negativo con errore di livello superiore imprevisto COMPILETODALVIK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 9df9e348440b9dd4b18b3859d64cbe47bd05b24c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Motivo per cui il progetto Xamarin.Forms.Maps Android esito negativo con errore di livello superiore imprevisto COMPILETODALVIK?

Questo errore può verificarsi nel riquadro di errore di Visual Studio per Mac o nella finestra di Output di compilazione di Visual Studio. nei progetti Android utilizzando Xamarin.Forms.Maps.

In genere, questo viene risolto aumentando le dimensioni dell'Heap Java per il progetto xamarin. Seguire questi passaggi per aumentare le dimensioni dell'heap:

## <a name="visual-studio"></a>Visual Studio

1. Fare clic sul progetto Android & aprire le opzioni del progetto.
2. Passare a **Android Opzioni -> Avanzate**
3. Nella casella di testo di dimensioni di heap Java immettere 1 GB.
4. Ricompilare il progetto.

![Schermata delle opzioni di progetto di Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "opzioni in Visual Studio di compilazione Android")

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1.  Fare clic sul progetto Android & aprire le opzioni del progetto.
2.  Passare a **Compila -> generazione Android -> Avanzate**
3.  Nella casella di testo di dimensioni di heap Java immettere 1 GB.
4.  Ricompilare il progetto.  

![Schermata di Visual Studio per Mac progetto opzioni](maps-compiletodalvik-error-images/xsjavaheap.png "Android genera opzioni in Visual Studio per Mac")

