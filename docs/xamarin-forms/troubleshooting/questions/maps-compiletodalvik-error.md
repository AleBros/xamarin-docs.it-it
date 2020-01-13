---
title: Perché il progetto Xamarin.Forms. Maps Android ha esito negativo con un errore di livello superiore imprevisto COMPILETODALVIK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 2872cc7b54e26d07b388f08d650048e8d3861930
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759973"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Perché il progetto Xamarin.Forms. Maps Android ha esito negativo con un errore di livello superiore imprevisto COMPILETODALVIK?

Questo errore può essere visualizzato nel riquadro degli errori di Visual Studio per Mac o nella finestra di output di compilazione di Visual Studio; nei progetti Android con Xamarin.Forms. maps.

Questo problema viene risolto in genere aumentando le dimensioni dell'heap Java per il progetto Xamarin.Android. Per aumentare le dimensioni dell'heap, attenersi alla procedura seguente:

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
