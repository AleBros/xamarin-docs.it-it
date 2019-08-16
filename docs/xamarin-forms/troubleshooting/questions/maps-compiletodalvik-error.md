---
title: Perché il progetto Novell. Forms. Maps Android ha esito negativo con un errore di livello superiore imprevisto COMPILETODALVIK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: efa21d3547167996e1a7dcc533caa00e0b1262e6
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529039"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Perché il progetto Novell. Forms. Maps Android ha esito negativo con un errore di livello superiore imprevisto COMPILETODALVIK?

Questo errore può essere visualizzato nel riquadro degli errori di Visual Studio per Mac o nella finestra di output di compilazione di Visual Studio; nei progetti Android con Novell. Forms. maps.

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

