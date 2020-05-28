---
title: Perché My Xamarin.Forms . Il progetto Android di Maps non riesce con un errore di livello superiore imprevisto COMPILETODALVIK?
ms.topic: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e29535e71cb77b05da41c043c6fd932ae4f5ce95
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135850"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Perché My Xamarin.Forms . Il progetto Android di Maps non riesce con un errore di livello superiore imprevisto COMPILETODALVIK?

Questo errore può essere visualizzato nel riquadro degli errori di Visual Studio per Mac o nella finestra di output di compilazione di Visual Studio; nei progetti Android usando Xamarin.Forms . Mappe.

Questo problema viene risolto in genere aumentando le dimensioni dell'heap Java per il progetto Novell. Android. Per aumentare le dimensioni dell'heap, attenersi alla procedura seguente:

## <a name="visual-studio"></a>Visual Studio

1. Fare clic con il pulsante destro del mouse sul progetto Android & aprire le opzioni del progetto.
2. Vai a **Opzioni Android-> avanzate**
3. Nella casella di testo dimensioni heap Java immettere 1G.
4. Ricompilare il progetto.

![Screenshot delle opzioni del progetto di Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Opzioni di compilazione Android in Visual Studio")

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Fare clic con il pulsante destro del mouse sul progetto Android & aprire le opzioni del progetto.
2. Passare a **Build-> Android Build-> Advanced**
3. Nella casella di testo dimensioni heap Java immettere 1G.
4. Ricompilare il progetto.  

![Screenshot delle opzioni del progetto Visual Studio per Mac](maps-compiletodalvik-error-images/xsjavaheap.png "Opzioni di compilazione Android in Visual Studio per Mac")
