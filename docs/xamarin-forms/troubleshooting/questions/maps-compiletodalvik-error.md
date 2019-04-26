---
title: Il motivo per cui il progetto Android verifica non riesce con errore di primo livello imprevisto COMPILETODALVIK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 9df9e348440b9dd4b18b3859d64cbe47bd05b24c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250459"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Il motivo per cui il progetto Android verifica non riesce con errore di primo livello imprevisto COMPILETODALVIK?

Questo errore potrebbe essere presente il riquadro errori di Visual Studio per Mac o nella finestra di Output di compilazione di Visual Studio. nei progetti Android tramite verifica.

Questo viene in genere risolto aumentando le dimensioni dell'Heap di Java per il progetto xamarin. Android. Seguire questi passaggi per aumentare le dimensioni dell'heap:

## <a name="visual-studio"></a>Visual Studio

1. Fare clic sul progetto Android e aprire le opzioni di progetto.
2. Passare a **opzioni Android -> Avanzate**
3. Nella casella di testo di dimensioni di heap Java immettere 1 GB.
4. Ricompilare il progetto.

![Screenshot delle opzioni di progetto di Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Android opzioni di compilazione in Visual Studio")

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1.  Fare clic sul progetto Android e aprire le opzioni di progetto.
2.  Passare a **Compila -> compilazione Android -> Avanzate**
3.  Nella casella di testo di dimensioni di heap Java immettere 1 GB.
4.  Ricompilare il progetto.  

![Screenshot di Visual Studio per Mac Project Options](maps-compiletodalvik-error-images/xsjavaheap.png "Android opzioni di compilazione in Visual Studio per Mac")

